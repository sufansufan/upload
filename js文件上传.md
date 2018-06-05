```
 let upload = document.querySelector('#thumb1');
    let hidden = document.querySelector('input[type=hidden]');
    let imgType = ['png','gif','jpeg','jpg'];
    let size = 5*1024*1024;
    upload.onchange = function() {
        /*特殊运算符...*/
        console.log(this.files);
        [...this.files].forEach((element, index) => {
            let eType = element.type.split('/')[1];
            console.log(element.type);
            if (!(element.size <= size && imgType.includes(eType))) {
                alert('请检查文件的类型和文件的大小');
                return;
            }
            let reader = new FileReader();
            reader.readAsDataURL(element);
            reader.onload = function (e) {
                let imgs = new Image();
                imgs.src = e.target.result;
                let imgBox = document.querySelector('.imgBox');
                imgBox.appendChild(imgs);

                let data = new FormData();
                data.append('file', element);
                let xml = new XMLHttpRequest();
                xml.upload.onprogress = function (e) {
                    document.querySelectorAll('.progress-bar')[index].style.width = `${e.loaded / e.total * 100}%`;
                };
                xml.open('post','/ktv/index.php/shopmanage/upload',true);
                xml.send(data);
                /*数据返回来*/
                xml.onload = function () {
                    hidden.value = xml.response;
                }
            }
        })
    }
```

