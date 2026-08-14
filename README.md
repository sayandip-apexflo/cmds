# cmds

chmod 600 ~/.apexedge/pki/agent.key

chmod 644 ~/.apexedge/pki/agent.crt ~/.apexedge/pki/ca.crt

Run from the node

openssl version

set -Eeuo pipefail
umask 077

controller_ip="192.168.1.10"
destination="./randomScript.sh"
temporary="$(mktemp "${destination}.part.XXXXXX")"

trap 'rm -f -- "${temporary}"' EXIT

openssl s_client \
  -connect "${controller_ip}:50052" \
  -cert "${HOME}/.apexedge/pki/agent.crt" \
  -key "${HOME}/.apexedge/pki/agent.key" \
  -CAfile "${HOME}/.apexedge/pki/ca.crt" \
  -verify_ip "${controller_ip}" \
  -verify_return_error \
  -quiet \
  </dev/null >"${temporary}"

test -s "${temporary}"
chmod 0700 "${temporary}"
mv -f -- "${temporary}" "${destination}"

trap - EXIT

echo "Bootstrap script received at ${destination}"
