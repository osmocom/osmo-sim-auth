= osmo-sim-auth =

This is a small script that can be used with a PC-based smart card
reader to obtain GSM/UMTS authentication parameters from a SIM/USIM
card.

== prerequisites ==

We assume that you have

* A smart card reader compatible with pcsc-lite
* Installed python program and pyscard library


=== smart card reader ===

Any reader supported by pcsc-lite will work.  However, a reader
compatible with the USB CCID device class is much recommended.

Please verify that the hardware and driver setup is working, e.g. by
using the 'pcsc_scan' tool included with pcsc-lite.  You should get an
output like:
{{{
V 1.4.17 (c) 2001-2009, Ludovic Rousseau <ludovic.rousseau@free.fr>
Compiled with PC/SC lite version: 1.5.5
Scanning present readers...
0: OmniKey CardMan 5121 00 00

Wed Dec  7 01:32:37 2011
 Reader 0: OmniKey CardMan 5121 00 00
  Card state: Card inserted, Shared Mode, 
  ATR: 3B 9F 95 80 1F C7 80 31 E0 73 FE 21 13 57 12 29 11 02 01 00 00 C2

ATR: 3B 9F 95 80 1F C7 80 31 E0 73 FE 21 13 57 12 29 11 02 01 00 00 C2
}}}

plus many more lines of output decoding the ATR.

If you only get 
{{{
PC/SC device scanner
V 1.4.17 (c) 2001-2009, Ludovic Rousseau <ludovic.rousseau@free.fr>
Compiled with PC/SC lite version: 1.5.5
Scanning present readers...
0: OmniKey CardMan 5121 00 00

Wed Dec  7 01:35:08 2011
 Reader 0: OmniKey CardMan 5121 00 00
  Card state: Card removed, 
}}}

then your card was not detected in the reader. 

If you don't even get any displayed readers, your hardware and/or driver
setup are likely wrong.


=== pyscard ===

pyscard can be installed from packages of major Linux distributions.

If you want to build it from source, it is available from
http://pyscard.sourceforge.net/


== running osmo-sim-auth ==

{{{
$ ./osmo-sim-auth.py --help
Usage: osmo-sim-auth.py [options]

Options:
  -h, --help            show this help message and exit
  -a AUTN, --autn=AUTN  AUTN parameter from AuC
  -r RAND, --rand=RAND  RAND parameter from AuC
  -d, --debug           Enable debug output
  -s, --sim             SIM mode (default: USIM)
}}}

you can run the program in two modes:
 * running GSM authentication (classic SIM card protocol)
 * running UMTS authentication (USIM card protocol)

=== classic GSM authentication ===

This mode will use the "RUN GSM ALGORITHM" command as specified in GMS
TS 11.11

You have to specify
 * the 16 byte RAND value from the AuC (-r) as 32 hex digits
 * the '-s' flag to enable SIM mode

{{{
$ ./osmo-sim-auth.py -r 00000000000000000000000000000000 -s
Testing SIM card with IMSI 901700000000403

GSM Authentication
SRES:   215fdb4d
Kc:     6de816a759a42912
}}}

=== UMTS authentication ===

This mode will use the "AUTHENTICATE" command as specified in 3GPP TS
31.102

You have to specify
 * the 16 byte RAND value from the AuC (-r) as 32 hex digits
 * the 16 byte AUTN value from the AuC (-a) as 32 hex digits

==== successful operation ====

In this case, the tool will output the following values obtained from
the card:
 * RES authentication result value
 * CK ciphering key
 * IK integrity key
 * Kc for inter-RAN handover from UMTS -> 2G

Secondly, the tool will re-run the authentication in "2G authentication
context" in order to obtain the SRES result.  This value would be used
if a 3G/2G dual-mode phone registers on a 2G network.

{{{
python ./osmo-sim-auth.py -r 00000000000000000000000000000000 -a ec9320c2c2000000e1dd22c1ad3e2d3d 
[+] UICC AID found:
found [AID 1] 3GPP || USIM || (255, 134) || (255, 255) || (137, 255,
255, 255, 255)
[+] USIM AID selection succeeded

Testing USIM card with IMSI 901700000000403

UMTS Authentication
RES:    e9fc88ccc8a35381
CK:     7200a184d8f2c758fbdf87900ddbf275
IK:     12cb2dd3e0ec8378f6fc1d606c619f47
Kc:     6de816a759a42912

GSM Authentication
SRES:   215fdb4d
Kc:     6de816a759a42912
}}}

==== synchronization required ====

In this case, the AUTHENTICATE command will return the AUTS parameter,
which has to be sent to the AuC in order to re-synchronzie the SQN
counter which is kept in both the USIM as well as the AuC.

{{{
./osmo-sim-auth.py -r 00000000000000000000000000000000 -a ec9320c2c2120000c8b7de2a3449f1bd
[+] UICC AID found:
found [AID 1] 3GPP || USIM || (255, 134) || (255, 255) || (137, 255,
255, 255, 255)
[+] USIM AID selection succeeded

Testing USIM card with IMSI 901700000000403

UMTS Authentication
AUTS:   8711a0ec9e2be2f766881a64605b

GSM Authentication
SRES:   215fdb4d
Kc:     6de816a759a42912
}}}
