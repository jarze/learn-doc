- [`docxtemplater`](https://docxtemplater.com/)
- [`docxtemplater [npm]`](https://www.npmjs.com/package/docxtemplater/)
- [`pizzip`](https://github.com/parallax/jsPDF)

```js
import Docxtemplater from 'docxtemplater';
import PizZip from 'pizzip';
import PizZipUtils from 'pizzip/utils/index.js';

export function downloadFile(data, filename) {
	const blob = new Blob([data]);
	if (typeof window.navigator.msSaveBlob !== 'undefined') {
		// IE workaround for "HTML7007: One or more blob URLs were
		// revoked by closing the blob for which they were created.
		// These URLs will no longer resolve as the data backing
		// the URL has been freed."
		window.navigator.msSaveBlob(blob, decodeURI(filename));
	} else {
		const blobURL = window.URL.createObjectURL(blob);
		const tempLink = document.createElement('a');
		tempLink.style.display = 'none';
		tempLink.href = blobURL;
		tempLink.setAttribute('download', decodeURI(filename));
		// Safari thinks _blank anchor are pop ups. We only want to set _blank
		// target if the browser does not support the HTML5 download attribute.
		// This allows you to download files in desktop safari if pop up blocking
		// is enabled.
		if (typeof tempLink.download === 'undefined') {
			tempLink.setAttribute('target', '_blank');
		}

		document.body.appendChild(tempLink);
		tempLink.click();
		document.body.removeChild(tempLink);
		window.URL.revokeObjectURL(blobURL);
	}
	return {};
}

function loadFile(url, callback) {
  PizZipUtils.getBinaryContent(url, callback)
}

interface GenerateDocxFunc {
  (
    /** 模版文件 */
    templateFile?: DocFiles,
    /** 模版填充数据 */
    data?: any,
    /** 导出文件名 */
    outputName?: string,
  ): Promise<{
    /** 文件名 */
    fileName: string
    /**  文件 */
    file: Blob
  }>
}

interface GenerateToDownloadDocxFunc {
  (...params: Parameters<GenerateDocxFunc>): Promise<any>
}

/**
 * @description: 生成docx文件
 * @param {string} templateFile 模版文件
 * @param {object} data 模版填充数据
 * @param {string} outputName 导出文件名
 * @return {Promise<{fileName: string, file: Blob}>} 文件名和文件
 */
export const generateDocx: GenerateDocxFunc = (
  templateFile,
  d,
  outputName = templateFile,
) =>
  new Promise((resolve, reject) => {
    const { url: templateUrl, placeholder = {} } = docxFilesMap[templateFile]
    if (!templateUrl) {
      reject(new Error('模版文件不存在'))
      return
    }
    const data = handlePlaceholderData(templateFile)(d, placeholder)

    loadFile(templateUrl, function (error, content) {
      if (error) {
        reject(error)
        return
      }
      var zip = new PizZip(content)
      var doc = new Docxtemplater(zip, {
        paragraphLoop: true,
        linebreaks: true,
      })
      doc.setData(data)
      try {
        // render the document (replace all occurences of {first_name} by John, {last_name} by Doe, ...)
        doc.render()
      } catch (error) {
        // The error thrown here contains additional information when logged with JSON.stringify (it contains a properties object containing all suberrors).
        function replaceErrors(key, value) {
          if (value instanceof Error) {
            return Object.getOwnPropertyNames(value).reduce(function (
              error,
              key,
            ) {
              error[key] = value[key]
              return error
            },
            {})
          }
          return value
        }
        console.log(JSON.stringify({ error: error }, replaceErrors))

        if (error.properties && error.properties.errors instanceof Array) {
          const errorMessages = error.properties.errors
            .map(function (error) {
              return error.properties.explanation
            })
            .join('\n')
          console.log('errorMessages', errorMessages)
          // errorMessages is a humanly readable message looking like this :
          // 'The tag beginning with "foobar" is unopened'
        }
        throw error
      }
      var out = doc.getZip().generate({
        type: 'blob',
        mimeType:
          'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
      }) //Output the document using Data-URI
      resolve({ fileName: `${outputName || 'doc'}.docx`, file: out })
    })
  })

/** 生成下载docx文件 */
export const generateToDownloadDocx: GenerateToDownloadDocxFunc = (...params) =>
  generateDocx(...params).then((r) => downloadFile(r.file, r.fileName))



```
