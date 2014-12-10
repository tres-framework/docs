# Installation for shared hosts

On a typical shared host, you have the following directory structure:

```
/
-- /public_html
```

*Because of Tres Framework's flexibility, it does not really matter if
`public_html` is named differently.*

If you are in this scenario, the following instructions apply to you:

1. Download Tres Framework
2. Extract the files to your web server.
3. [CHMOD][chmod] `/app/storage/` to have read, write and execute permissions
   (0777).
4. Test the application by going to the homepage.

If you are not in this scenario, check if your host is listed
[here][shared hosts]. If not, contact your host.

## Troubleshooting

If you have any problems, please see [troubleshooting][troubleshooting].

[chmod]: ../../misc/changing-file-permissions.md
[shared hosts]: ../README.md#shared-hosts
[troubleshooting]: ../troubleshooting.md
