[comment]: <> (SPDX-License-Identifier: AGPL-3.0)

[comment]: <> (--------------------------------------------------)
[comment]: <> (Copyright © 2024, 2025, 2026  Pellegrino Prevete)
[comment]: <> (All rights reserved)
[comment]: <> (--------------------------------------------------)

[comment]: <> (This program is free software: you can redistribute)
[comment]: <> (it and/or modify it under the terms of the GNU Affero)
[comment]: <> (General Public License as published by the Free)
[comment]: <> (Software Foundation, either version 3 of the License.)

[comment]: <> (This program is distributed in the hope that it)
[comment]: <> (will be useful, but WITHOUT ANY WARRANTY;)
[comment]: <> (without even the implied warranty of)
[comment]: <> (MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.)
[comment]: <> (See the)
[comment]: <> (GNU Affero General Public License for more details.)

[comment]: <> (You should have received a copy of)
[comment]: <> (the GNU Affero General Public)
[comment]: <> (License along with this program.)
[comment]: <> (If not, see <https://www.gnu.org/licenses/>.)


# DynSSH (`dynssh`)

SSH wrapper which allows
to establish various SSH connections
towards various types of dynamical
hosts.

In particular it is able to connect
to hosts provided by
[`hotspot-resolver`](
  https://github.com/themartiancompany/hotspot-resolver)
and
[`tor-resolver`](
  https://github.com/themartiancompany/tor-resolver),
which means to Android devices through Android
device hotspots seamlessly and to devices
available on hidden services (`address.onion`).

It depends on
[`ssh-utils`](
  https://github.com/themartiancompany/ssh-utils)
and it's written using the
[Crash Bash](
  https://github.com/themartiancompany/crash-bash)
library.

It's able to connect to hosts using
`autossh`, `sshfs`, `rsync`

## License

Released by Pellegrino Prevete under the terms of the
GNU Affero General Public license version 3.
