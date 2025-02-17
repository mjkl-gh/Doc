
APC UPS are configured for bootp. This will not show up in the dhcp leases, but will show up in the logs

To use DHCP with default settings a magic packet is required. in Opnsense:

```
option 43 : String : 01:04:31:41:50:43
```

To reset the AP7920 PDU:

1. Press and hold the reset button for about 10 seconds.
2. The status light should blink rapidly.
3. Release the reset button then press it again for about a second.
4. The light should go orange.
5. After about 20 seconds the reset will complete and the device will have booted.
6. Using your serial terminal at 9600 baud, hit enter a few times until you see the login prompt.
7. Use `apc` / `apc` to login. You can now configure IP settings/reset passwords etc.

