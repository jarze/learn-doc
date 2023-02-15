
- [`html2canvas`](https://html2canvas.hertzen.com/)
- [`jspdf`](https://github.com/parallax/jsPDF)

```js
import html2canvas from 'html2canvas';
import { jsPDF } from 'jspdf';

//默认A4纸A4纸大小21*29.7cm
const a4Size = { width: 21, height: 29.7 };

const checkExceedMaxPage = (element, maxPage: number = 9) => {
  return element?.clientHeight / ((a4Size.height * element?.clientWidth) / a4Size.width) > maxPage;
};

export const selectorToPdf = ({
  selector,
  scale = 1,
  pagination = true,
  fileName = 'pdf',
}: {
  /* 导出dom节点，注意selector 的子节点宽度需与父节点相同 */
  selector: HTMLElement;
  scale?: number;
  /* 导出的pdf单页太过长会出现导出不完全的问题；若内容太长以子节点切割分页*/
  pagination?: boolean;
  fileName?: string;
}) =>
  new Promise((resolve, reject) => {
    // 超过一定页数长度分节点进行处理，否则会有导出不完全的情况
    const nodes = (
      checkExceedMaxPage(selector) ? [...Array.from(selector.childNodes)] : [selector]
    ).filter((i: any) => !!i?.clientHeight);
    setTimeout(() => {
      Promise.all(nodes.map(e => html2canvas(e as any, { scale })))
        .then(canvasArray => {
          let doc;
          let currentY = 0;
          canvasArray.forEach((canvas, i) => {
            const { width, height } = canvas;
            let ratio = a4Size.width / width;
            const [w, h] = [width * ratio, height * ratio];

            let pageFormat = [w, h > a4Size.height ? h : a4Size.height];
            if (!i) {
              if (!pagination) {
                pageFormat = [
                  a4Size.width,
                  (selector?.clientHeight * a4Size.width) / selector?.clientWidth,
                ];
              }
              doc = new jsPDF({
                unit: 'cm',
                format: pageFormat,
                compress: true,
              });
            } else if (pagination) {
              doc.addPage(pageFormat);
              currentY = 0;
            }
            var img = new Image();
            img.src = canvas.toDataURL('image/jpeg', 1);
            if (h > 0) {
              doc.addImage({
                imageData: img.src,
                format: 'JPEG',
                x: 0,
                y: currentY,
                width: w,
                height: h,
              });
              currentY += h;
            }
          });
          doc.save(fileName + '.pdf', { returnPromise: true }).then(resolve);
        })
        .catch(reject);
    }, 300);
  });

```