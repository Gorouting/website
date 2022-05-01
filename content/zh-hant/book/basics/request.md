---
title: "Request"
linkTitle: "Request"
weight: 1030
menu:
  book:
    parent: "basics"
---

在 Salvo 中可以通過 ```Request``` 獲取用戶請求的數據:

```rust
#[fn_handler]
async fn hello(req: &mut Request) -> String {
    req.get_param::<String>("id")
}
```

## 獲取查詢參數

可以通過 ```get_query``` 獲取查詢參數:

```rust
req.get_query::<String>("id");
```

## 獲取 Form 數據

可以通過 ```get_form``` 獲取查詢參數, 此函數為異步函數:

```rust
req.get_form::<String>("id").await;
```


## 獲取 JSON 反序列化數據

```rust
req.read::<User>().await;
```

## File uploading

```rust
#[fn_handler]
async fn upload(req: &mut Request, res: &mut Response) {
    let file = req.get_file("file").await;
    if let Some(file) = file {
        let dest = format!("temp/{}", file.file_name().unwrap_or_else(|| "file".into()));
        println!("{}", dest);
        if let Err(e) = std::fs::copy(&file.path(), Path::new(&dest)) {
            res.set_status_code(StatusCode::INTERNAL_SERVER_ERROR);
            res.render(format!("file not found in request: {}", e.to_string()));
        } else {
            res.render(format!("File uploaded to {}", dest));
        }
    } else {
        res.set_status_code(StatusCode::BAD_REQUEST);
        res.render("file not found in request");
    }
}
```