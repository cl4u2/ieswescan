#!/bin/sh

# Copyright (c) Claudio Pisa <clauz@ninux.org>
# Released under the GNU General Public License v3

# This scripts generates a "vendor_elements" configuration for hostapd.conf.

HNAME="$1"

if [ -z "$HNAME" ]; then
    echo -e "\t\tUsage: $0 <hostname>"
    exit 1
fi

len="$(echo -n "$HNAME" | wc -c)"
if [ "$len" -gt 14 ]; then
    echo "hostname too long. Max 14 chars"
    exit 1
fi

hexhname="$(echo -n "$HNAME" | sed 's/\(.\)/\1\n/g' | while read c; do printf '%02x' "'$c "; done)"

len=$((len+24))


hexlen=$(printf '%02x' $len)

vendor_elements="dd${hexlen}000c42000000011e000000001f660902ff0f${hexhname}000000000000"
echo $vendor_elements

