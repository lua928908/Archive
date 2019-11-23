# multer - 파일 업로드 관리

### multer란

multer는 파일 업로드를 위해 사용되는 multipart/form-data 를 다루기 위한 node.js 의 미들웨어 이다.
효율성을 최대화 하기 위해 busboy 를 기반으로 하고 있습니다
multer는 multipart (multipart/form-data)가 아닌 폼에서는 사용할 수 없다.


## 파일 업로드 구현하기

#### 설치하기
```
npm install --save multer
```
passport 모듈을 다운로드 받는다.

#### Multer 사용하기
``` 
    const multer = require('multer');
    const upload = multer({
        storage: multer.diskStorage({
            destination(req, file, cb){ // 파일이 저장될 경로, cb = callback
                cb(null, 'uploads/');
            },
            filename(req, file, cb){ // 파일의 이름을 지정
                const ext = path.extname(file.originalname);
                cb( null, path.basename(file.originalname, ext) + new Date().valueOf() + ext )
            }
        }),
        limit: { fileSize: 5*1024 * 1024 }, // limit: 파일사이즈(바이트 단위), 5MB 까지 업로드
    });
```
1. path.extname()은 인자의 확장자만을 가져온다 예) .js
2. path.basename()은 인자의 파일이름만을 가져온다 예) example.js
3. basename()으로 파일명을 가져올때 두번째인자가 있으면, 두번째 인자를 뺀 나머지 값만 리턴한다.
4. 예) path.basename( 'index.html', 'html' ) -> index
     
path.basename(file.originalname, ext)를 이용해서 순수 파일명을 추출해야
2019.01.29.txt 와같이 파일명에 dot이 들어가는경우 파일명을 잘못 추출하는것을 막을 수 있다.

#### router
```
    router.post('/img', isLoggedIn, upload.single('img'), (req, res, next)=>{ // upload.single('img') -> 파일을 올리는 input태그의 name값
      // 보통 form을통해 요청을 보내면 req.body에 담기게되는데 multer를 이용해 이미지를 올리면 req.file에 담긴다.
      console.log(req.file);
      res.json({ url: `/img/${req.file.filename}` });
    });
```

#### 서버로 전달하기 - onChange 핸들러
```
    const onChangeImages = useCallback((e) => {
        console.log('e.target', e.target);
        const imageFormData = new FormData();
        [].forEach.call(e.target.files, (f) => {
            imageFormData.append('image', f); // upload.array() 할때 key의 이름이 같아야함.
        });
        dispatch({
            type: 'UPLOAD_IMAGES_REQUEST',
            data: imageFormData,
        });
        console.log('최종 imageFormData = ', imageFormData);
    }, []);
```
input에 onChange 이벤트핸들러로 위에함수를 전달하면된다.

```
req.file  => upload.single()을 통해 요청한 경우 file로 받는다
req.files => upload.array()를 통해 요청한 경우 files로 받는다
```
node backend 에서는 req.file 혹은 eq.files를 통해서 사용자가 업로드한 이미지를 받을 수 있다. 


##API - options

| 값 | 의미 | 기본값 |
|---|:---:|---:|
| `fieldname` | 폼에 정의된 필드 명	 |  |
| `originalname` | 사용자가 업로드한 파일 명	 |  |
| `encoding` | 파일의 엔코딩 타입 |  |
| `mimetype` | 파일의 Mime 타입 |  |
| `size` | 파일의 바이트(byte) 사이즈 |  |
| `destination` | 파일이 저장된 폴더 | DiskStorage |
| `filename` | destination 에 저장된 파일 명 | DiskStorage |
| `path` | 업로드된 파일의 전체 경로 | DiskStorage |
| `buffer` | 전체 파일의 Buffer | MemoryStorage |

## API - limits
| 속성 | 설명 | 기본값 |
|---|:---:|---:|
| `fieldNameSize` | 필드명 사이즈 최대값 | 100 bytes |
| `fieldSize` | 필드값 사이즈 최대값 | 1MB |
| `fields` | 파일형식이 아닌 필드의 최대 개수 | 무제한 |
| `fileSize` | multipart 형식 폼에서 최대 파일 사이즈(bytes) | 무제한 |
| `files` | multipart 형식 폼에서 파일 필드의 최대 개수 | 무제한 |
| `parts` | for multipart forms, the max number of parts (fields + files) | 무제한 |
| `headerPairs` | multipart 형식 폼에서 파싱할 헤더의 key=>value 쌍의 최대 개수 | 2000 |

| `mimetype` | 파일의 Mime 타입 |  |
| `size` | 파일의 바이트(byte) 사이즈 |  |
| `destination` | 파일이 저장된 폴더 | DiskStorage |
| `filename` | destination 에 저장된 파일 명 | DiskStorage |
| `path` | 업로드된 파일의 전체 경로 | DiskStorage |
| `buffer` | 전체 파일의 Buffer | MemoryStorage |