# PCMA 16 for Asterisk

RFC 3551 [chapter 4.5](http://tools.ietf.org/html/rfc3551#section-4.5) and RFC 4856 [chapter 2.1.17](http://tools.ietf.org/html/rfc4856#section-2.1.17) mention it briefly: The rate of PCMA is not fixed at 8000 samples per minute but can vary. In the year 2007, the German company AVM added PCMA/16000 to their Internet access device FRITZ!Box 7170 to enable HD Voice for their Wi-Fi phone FRITZ!Mini. AVM called this ‘PCMA 16’. The same was done for PCMU, which was called ‘Linear PCM 16’. In the log at ‘fritz.box → Telefonie → Eigene Rufnummern → Sprachübertragung’, AVM calls this G.711-HD.

This is not G.711-WB ([G.711.1](http://en.wikipedia.org/wiki/G.711#G.711.1)), also called PCMA-WB ([RFC 5391](http://tools.ietf.org/html/rfc5391#section-5.3.1)). Instead, the 16000 samples of PCMA 16 simply double the bit-rate of PCMx/8000. Still, AVM did not increase the size of each data packet, because AVM uses 10 milliseconds as packetization time (ptime) always – even when `ptime=20` was negotiated via SIP/SDP.

This is an implementation of PCMA 16 and allows pass-through between two FRITZ!Boxes and optionally transcoding to/from other audio formats (which is done by a FRITZ!Box normally).

In the same year, G.722 was chosen in DECT/CAT-iq to replace G.726-32 and enable HD Voice. Wi-Fi handsets got replaced by DECT handsets. AVM went for DECT and G.722 in their FRITZ!Box 7270, just one year later. Nevertheless even today, AVM products offer PCMA/16000 and PCMU/16000; why ever.

This implementation is for testing purposes, whether your network allows a RTP stream of 128 kbit per seconds (plus the RTP, UDP, and IP overhead) and no [traffic shaping](http://en.wikipedia.org/wiki/Traffic_shaping) kicks in. Furthermore, it can be used for interoperability testing because some media gateways are known to ignore the sampling rate and threat PCMA/16000 like PCMA/8000. Beside that, I am not aware of any practical use, because all HD-Voice enabled FRITZ!Boxes transcode G.722 as well. If you know an additional use-case, please, drop me an E-mail – I am curious.

## Installing the patch

At least Asterisk 13 is required. These changes were last tested with Asterisk 13.22 (and Asterisk 16.0). If you use a newer version and the patch fails, please, [report](https://help.github.com/articles/creating-an-issue/)!

	cd /usr/src/
	wget downloads.asterisk.org/pub/telephony/asterisk/asterisk-13-current.tar.gz
	tar zxf ./asterisk*
	cd ./asterisk*
	sudo apt --no-install-recommends --assume-yes install build-essential pkg-config libedit-dev libjansson-dev libsqlite3-dev uuid-dev libxslt1-dev xmlstarlet

Apply the patch:

	wget github.com/traud/asterisk-alaw16/archive/master.zip
	unzip -qq master.zip
	rm master.zip
	cp --verbose --recursive ./asterisk-alaw16*/* ./
	patch -p0 <./alaw16_pass_through.patch
	patch -p0 <./alaw16_transcoding.patch

Configure your patched Asterisk:

	./configure

Enable slin16 in menuselect for transcoding, for example via:

	make menuselect.makeopts
	./menuselect/menuselect --enable-category MENUSELECT_CORE_SOUNDS

Compile and install:

	make
	sudo make install

## Thanks go to
* [Guido Reismüller](http://www.linkedin.com/in/guido-reism%25C3%25BCller-4a392943) (at that time with AVM) who explained PCMA 16 in the [Asterisk bug tracker…](http://bugs.digium.com/view.php?id=10331)
* Asterisk team: Thanks to their efforts and architecture, I was able to add this format to Asterisk in less than three hours. Writing this Read Me took longer!
