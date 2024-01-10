#!/usr/bin/env bash
#
# SPDX-License-Identifier: AGPL-3.0

_path="$( \
  export \
    _OLDPWD="$(pwd)" && \
    cd \
      "$(dirname \
           "${BASH_SOURCE[0]}")" && \
    echo\
      "$(pwd)" && \
    cd "${_OLDPWD}")" && \
    unset \
      _OLDPWD

app_name="$( \
  basename \
    "${BASH_SOURCE[0]}")"

# Local ssh resolver
# $1: hostname of the device to connect
# $2: network device name
_dynssh() {
  local \
    _host="${1}" \
    _dev="${2}" \
    _user="${3}" \
    _port="${4}" \
    _args=() \
    _msg=() \
    _sshcfg \
    _cfg \
    _hostname \
    _address \
    _ns
  shift \
    4
  _args=(
    "$@")
  _sshcfg="${HOME}/.config/dynssh/ssh.config"
  [[ $(ifconfig \
         -a | \
         grep \
           "${_dev}") ]] && \
    _address="$( \
      _get_address \
        "${_dev}")"
  [[ "${_address}" == "" ]] && \
      _msg=(
	"${_dev} is not connected"
        "to a network.") && \
      echo \
        "${_msg[*]}" && \
      return \
        1
  _msg=(
    "${_dev} has address"
    "${_address}")
  echo \
    "${_msg[*]}"
  _ns="$( \
    _get_subnet \
      "${_address}")"
  [[ "$(_confirm_subnet \
          "${_dev}" \
          "${_ns}")" != "" ]] && \
    echo \
      "Error: anomalous behaviour" && \
    return \
      1
  [[ "${_ns}" == "" ]] && \
    _msg=(
      "${_dev} is not connected"
      "to a network.") && \
    echo \
      "${_msg[*]}" && \
    return \
      1
  _msg=(
    "${_dev} is on subnet"
    "${ns}.0")
  _read_conf \
    "${_dev}"
  _hostname="$( \
    echo \
      "${_cfg}" | \
      grep "${_host}=" | \
        awk \
	  -F "=" \
	  '{print $2}')"
  [[ "${_hostname}" == "" ]] && \
    echo \
      "Error: No address configured for ${_host}" && \
    exit \
      1
  _port="$( \
    echo \
      "${_hostname}" | \
      awk \
        -F ":" \
	'{print $2}')"
  _hostname="$( \
    echo \
      "${_hostname}" | \
      awk \
        -F ":" \
	'{print $1}')"
  _msg=(
    "Connecting to user ${_user} at ${_host}"
    "on ${_hostname} and port ${_port}")
  echo \
    "${_msg[*]}"
  _msg=(
    "Command: ${_args[@]}")
  echo \
    "${_msg[*]}"
  _ssh_conf \
    "${_host}" \
    "${_ns}.${_hostname}" \
    "${_user}" \
    "${_port}"
  ssh \
    -F "${_sshcfg}" \
    "${_host}" \
    "${_args[@]}"
}

# Writes SSH configuration
_ssh_conf() {
  local \
    _host="${1}" \
    _hostname="${2}" \
    _user="${3}" \
    _port="${4}" \
    _out \
    _cfg=()
  _out="${HOME}/.config/dynssh/ssh.config"
  _cfg=(
    "Host ${_host}"
    "  HostName ${_hostname}"
    "  Port ${_port}"
    "  IdentityFile ${HOME}/.ssh/${_host}"
    "  User ${_user}"
  )
  printf \
    '%s\n' \
    "${_cfg[@]}" > \
    "${HOME}/.config/dynssh/ssh.config"
  chmod \
    700 \
    "${_out}"
}

# Get subnet from IPv4 address
# $1: address
_get_subnet() {
  local \
    _address="${1}"
  echo \
    "${_address%.*}"
}

_confirm_subnet() {
  local \
    _dev="${1}" \
    _ns="${2}" \
    _confirm \
    _no_route=()
  _no_route=(
    "INET (IPv4) not configured"
    "in this system.")
  _confirm="$( \
    _get_subnet \
      "$(route | \
           grep \
	     "${_dev}" | \
	     awk \
	       '{print $1}')")"
  [[  "$(route)" == \
      "${_no_route[*]}" ]] && \
    return
  [[ "${_ns}" != \
     "${_confirm}" ]] && \
    echo \
      "ifconfig: ${_ns}" && \
    echo \
      "route: ${_confirm}"
}

_get_address() {
  local \
    _dev="${1}" \
    _line
  _line="$( \
    ifconfig \
      -a | \
      grep \
        -Pn \
        "${_dev}" | \
        cut \
          -d":" \
          -f 1)"
  _line=$(( \
    _line + 1 ))
  ifconfig \
    -a | \
      sed \
        -n "${_line}p;" | \
	awk '{print $2}'
}

_check_conf() {
  local \
    _conf="${1}" \
    _address \
    _dir \
    _host \
    _perm
  _dir="${HOME}/.config"
  _conf="${_dir}/${app_name}/localhosts.cfg"
  _msg=(
    "Configuration file:"
    "${_conf}"
  )
  echo \
    "${_msg[*]}"
  [ ! -e  "${_conf}" ] && \
    echo \
      "$( \
        basename \
          "${_conf}") does not exist" && \
    mkdir \
      -p \
      "$(dirname \
           "${_conf}")" && \
    echo \
      "# values go from 2 to 254" >> \
      "${_conf}" && \
    echo \
      "# device=111" >> \
      "${_conf}" && \
    _input_credentials && \
    _address="$( \
      _get_address \
        "${_dev}")" && \
    echo \
      "${_host}=${_address##*.}:${_port}" >> \
      "${_conf}"

  _perm="$( \
    stat \
      -c '%a' \
      "${_conf}")"
  [[ "${_perm}" != "700" ]] && \
    chmod \
      700 \
      "${_conf}"
}

_input_credentials() {
  echo \
    "enter name for this host:" && \
  while \
    [[ "${_host}" == "" ]]; do
    read \
      _host
  done
  echo \
    "enter port for this host:" && \
  while \
    [[ "${_port}" == "" ]]; do
    read \
      _port
   done
}

_read_conf() {
  local \
    _dev="${1}" \
    _conf \
    _dir \
    _hostname=""
  _dir="${HOME}/.config"
  _conf="${_dir}/${app_name}/localhosts.cfg"
  _check_conf \
    "${_conf}"
  _cfg="$( \
    cat \
      "${_conf}")"
  _cfg="$(\
    echo \
      "${_cfg}" | \
      sed \
        -e "/# */d" | \
	grep \
          "=" )"
  [[ "${_cfg}" == '' ]] && \
    echo \
      "ERROR: empty configuration file!" && \
    return \
      1
}

_host="${1}"
_dev="${2}"
_user="${3}"
_port="${4}"
shift 4
_args=(
  "$@"
)

[[ "${_dev}" == "" ]] && \
  _dev='wlan0' && \
  echo "Info: connecting on ${_dev}"

[[ "${_user}" == "" ]] || \
[ ! -n "${TERMUX_VERSION}" ] && \
  _user='dev' && \
  echo "Info: connecting with user ${_user}"

[[ "${_port}" == "" ]] && \
  _port='2222' && \
  echo "Info: connecting on port ${_port}"

[[ "${_host}" == "" ]] && \
  echo "Usage: ${app_name} <host> <device>" && \
  exit 1

_dynssh \
  "${_host}" \
  "${_dev}" \
  "${_user}" \
  "${_port}" \
  "${_args[@]}"

# vim:set sw=2 sts=-1 et:
