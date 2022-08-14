# macOS ansible playbook

Here a playbook which installs and updates MacPorts, also it may optionally
bootstrap MacPorts to get the last version of curl, and updates the system on
defined list of macOS machines.

The main goal is install MacPorts which is linked against `curl` inside
bootstrap MacPorts to fix not working SSL. See: https://trac.macports.org/ticket/51516

It also forces ansible to use a python from bootstrap MacPorts that allows to
support old systems like 10.5.

This playbook supports integration with Parallels which to make a snapshot for
each update, and zeros freespace to save your host disk :)

To use it add your ssh key into `keys` folder with used username and create
`hosts` files like:

```
[macos]
macos-10.5-leopard              prl_id=b7151a56-371b-4b63-87db-6533e00da1da
macos-10.6-snow-leopard         prl_id=e7448af8-cbfa-4560-be77-dd0770ac09dd
macos-10.7-lion                 prl_id=f0b1c3ca-94a8-477a-87de-9eec40c377b9
macos-10.8-mountain-lion        prl_id=f4dec53b-b5ac-4bbd-bc08-c1ac32a42149
macos-10.9-mavericks            prl_id=276f0cd8-6111-412a-8eb0-60d0c95ff3fd
macos-10.10-yosemite            prl_id=6c015234-ff64-4755-8f92-811ae9549aca
macos-10.11-el-capitan          prl_id=ec579726-d901-4ad0-9ea5-65f04fe0a8ec
macos-10.12-sierra              prl_id=3212426b-c412-4c64-9de1-d9f9016144fb
macos-10.13-high-sierra         prl_id=7b1e3cd9-58e0-4c99-bda7-377efba195d5
macos-10.14-mojave              prl_id=1033dba8-f5fc-47b8-adf1-19ac8390b87a
macos-10.15-catalina            prl_id=0d3de040-2ef0-43f0-8d46-4046c6d59198
macos-11-bigsur                 prl_id=04b5fb7d-90bb-41ba-965a-f7312c692d9e
macos-12-monterey               prl_id=b03b0f30-0158-4fe6-ab6a-2197d1d6a24b

[bootstrap_curl]
macos-10.5-leopard
macos-10.6-snow-leopard
macos-10.7-lion
macos-10.8-mountain-lion
macos-10.9-mavericks

[bootstrap_python]
macos-10.5-leopard
macos-10.6-snow-leopard

[empty_macports_ports_path]
macos-10.5-leopard

[empty_macports_ports_path:vars]
macports_ports_path=

[bootstrap_curl:vars]
bootstrap_curl=curl

[bootstrap_python:vars]
bootstrap_python=python27-bootstrap
ansible_python_interpreter=/opt/bootstrap/libexec/python27-bootstrap/bin/python2.7
```
where `prl_id` is optional variable which contains a unique ID of Parallels' VM.

After that a simple run `ansible-playbook maintenance.yml` start to update
everything.

You may see that switching ports tree was disabled for 10.5; by default it
switches ports tree to `file:///Volumes/SharedFolders/Home/src/macports-ports`
with enabled `nosync` option.

I also enable a custom python for 10.5 and using curl up to 10.10.

Thus, it starts VM one-by-one, but if you have enough resources to run more VMs,
feel free to increase ansible serial. For example `ansible-playbook -e serial=2
maintenance.yml` runs two VM in parallel.

You may skip installing updates from Apple, cleanup or shrink the disk by
define variable `skip_updates`, `skip_cleanup_disk` or `skip_shrink_disk`.
