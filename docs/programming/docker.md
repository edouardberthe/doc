## Update all images

```
di --format "{{.Repository}}:{{.Tag}}" | xargs -L1 docker pull
```