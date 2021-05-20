# Show us your ID [200 pts]

**Category:** Misc
**Solves:** 56

## Description
>Analysis of a PDF file. Beware of rabbit holes.

**Hint**
* solution is not the most obvious one, but simple. PDF tools might not be most useful. Need to inspect deeper. Knowledge of PDF objects is helpful

## Solution
Denne var enklere enn jeg hadde trodd, men klarte ikke å løse den før etter at CTFen var over og med litt hjelp fra en writeup.

Hintet i tittelen er jo egentlig ganske beskrivende: `ID`. Så, sjekker etter `ID`med `strings`:
```zsh
      

$ strings nyan.pdf|grep -i id

/K**id**s \[ 3 0 R \]

/W**id**th 613

slice**ID**long

group**ID**long

" **id**\="646374667b3362306261347d"?> <x:xmpmeta xmlns:x="adobe:ns:meta/" x:xmptk="Adobe XMP Core 5.6-c111 79.158325, 2015/09/10-01:10:20 "> <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"> <rdf:Description rdf:about="" xmlns:xmpMM="http://ns.adobe.com/xap/1.0/mm/" xmlns:stEvt="http://ns.adobe.com/xap/1.0/sType/ResourceEvent#" xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:photoshop="http://ns.adobe.com/photoshop/1.0/" xmlns:xmp="http://ns.adobe.com/xap/1.0/" xmpMM:Document**ID**\="E5DE28DC62AFBBA8D54ED4260CA8F582" xmpMM:Instance**ID**\="xmp.i**id**:8d5ed2dc-16d8-4b7b-ab48-b50ceffc44db" xmpMM:OriginalDocument**ID**\="E5DE28DC62AFBBA8D54ED4260CA8F582" dc:format="image/jpeg" photoshop:ColorMode="3" xmp:CreateDate="2017-06-29T11:36:29-04:00" xmp:ModifyDate="2017-06-29T11:37:31-04:00" xmp:MetadataDate="2017-06-29T11:37:31-04:00"> <xmpMM:History> <rdf:Seq> <rdf:li stEvt:action="saved" stEvt:instance**ID**\="xmp.i**id**:8d5ed2dc-16d8-4b7b-ab48-b50ceffc44db" stEvt:when="2017-06-29T11:37:31-04:00" stEvt:softwareAgent="Adobe Photoshop CC 2015 (Macintosh)" stEvt:changed="/"/> </rdf:Seq> </xmpMM:History> </rdf:Description> </rdf:RDF> </x:xmpmeta> <?xpacket end="w"?>

/W**id**th 106

/**ID** \[<2c4eb8d0f54ba6f26ce6a1b302c1780c5249f9c2263a69d323feadc98595f3f9> <2c4eb8d0f54ba6f26ce6a1b302c1780c5249f9c2263a69d323feadc98595f3f9>\]
```

To av linjene ser mistenkelig ut som `hex`:
```zsh
$ pwn unhex '646374667b3362306261347d'
dctf{3b0ba4}
```

### Flag
> dctf{3b0ba4}
