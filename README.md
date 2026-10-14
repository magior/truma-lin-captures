# truma-lin-captures

Unofficial, independent LIN bus captures from a Truma CP Plus (C3) and a Combi 6 Gas, in readable
form, meant as a reference for open-source projects.

## Setup

| | |
|---|---|
| Panel | CP Plus, software C3.00.00 |
| Heater | Combi 6 Gas, software H5.00.00 |
| iNet Box | emulated by an ESP32 with a TJA1021 LIN transceiver, running the ESPHome `truma_inetbox` component |
| Recording | the ESP32 itself, every frame it saw on the bus (9600 baud), timestamped with its own clock |

## Logs

| File | What it shows |
|---|---|
| [`logs/heater-device-list.log`](logs/heater-device-list.log) | Device list sent by the CP Plus after registration |
| [`logs/fan-high-eco.log`](logs/fan-high-eco.log) | Fan "high" then "eco" |

Each log starts with a short summary. They are excerpts (a few seconds) of longer captures.

## Reading a log

```
    t (s)  id  data                     by   decoded
    7.334  18  ff ff ff ff ff ff ff ff  box  box: update pending
    7.486  3c  03 10 0b ba 00 1f 00 1e
    7.537  3c  03 21 00 00 22 ff ff ff
               └ M>S NAD 03 Truma-upload-request(BA): 00 1f 00 1e 00 00 22 ff ff ff
```

- `t (s)`: seconds from the start of the capture.
- `id`: LIN frame id. `03`–`09` are sent by the CP Plus to the heater, `16` is the heater's status,
  `18` is the iNet Box update flag, `3c`/`3d` are the diagnostic master request / slave response
  frames that carry the transport layer.
- `data`: the 8 data bytes (checksum not shown, only checksum-valid frames are listed);
  `(no answer)` when nobody answered the header.
- `by`: `box` when the answer came from the emulated iNet Box.
- `decoded`: signals of the frames we know.
- `└` lines: a complete transport-layer message, reassembled from the `3c`/`3d` frames above it:
  direction (`M>S` CP Plus to device, `S>M` device to CP Plus), NAD (`01` heater, `03` iNet Box),
  service, and for the iNet Box buffers their id and body; `->` gives the meaning where known
  (command uploaded, ack, device list entry).

## Notice

This is an independent, unofficial interoperability research project. Truma, iNet Box, CP Plus,
Combi and related product names are used solely to identify the products studied. All trademarks
belong to their respective owners. This project is not affiliated with, endorsed, sponsored,
approved or certified by Truma Gerätetechnik GmbH & Co. KG.

The captures were obtained by observing the bus of the author's own installation; no firmware was
read or decompiled and no protection was bypassed. They are not Truma documentation.

The logs contain control commands for a gas heater. Do not replay them on a real bus unless you
know what you are doing; this repository is not a safety system and comes with no warranty.

## Licence

The logs are dedicated to the public domain under [CC0 1.0](LICENSE).
