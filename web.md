# Web related tricks

## `POST` by `curl`
Ref: <https://stackoverflow.com/questions/4797534/how-do-i-manually-fire-http-post-requests-with-firefox-or-chrome>
```bash
curl -i -X POST http://m-c02w82yrhtdd:8080/economic-model/rest/us/wm/returns/calculation

curl -i -X GET http://rest-api.io/items
curl -i -X GET http://rest-api.io/items/5069b47aa892630aae059584

curl -i -X DELETE http://rest-api.io/items/5069b47aa892630aae059584

curl -i -X POST -H 'Content-Type: application/json' -d '{"name": "New item", "year": "2009"}' http://rest-api.io/items

curl -i -X PUT -H 'Content-Type: application/json' -d '{"name": "Updated item", "year": "2010"}' http://rest-api.io/items/5069b47aa892630aae059584
```


## Download file by `curl`
```
$ curl -O http://xxx/18.04/yyy.iso -x http://xxx.com:8080
```