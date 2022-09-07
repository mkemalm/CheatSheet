# What is OpenSCAP?

A tool which provides compliance and vulnerability scanning on servers.

https://www.open-scap.org/

# Installing

RHEL, Centos;

`yum install openscap-scanner scap-security-guide bzip2`

Ubuntu;

`apt install libopenscap8 bzip2`

Scap content will be installed to /usr/share/xml/scap/ssg/content with scap-security-guide package. https://github.com/ComplianceAsCode/content/releases can be used for other distros.

# Compliance Scanning

A compliance profile should be decided before scanning. List of compliances can be retrieved using xml filed within scap data. For example for RHEL;

`oscap info /usr/share/xml/scap/ssg/content/ssg-rhel8-ds.xml`

This will list many compliances, assume that we choose xccdf_org.ssgproject.content_profile_cis which is CIS compliance. We can scan with this command;

`oscap xccdf eval --profile xccdf_org.ssgproject.content_profile_cis --results-arf results.xml --report report.html /usr/share/xml/scap/ssg/content/ssg-rhel8-ds.xml`

This will generate report.html, we can download and browse it.

# Vulnerability Scanning

We should download OVAL definition file.

For example for RHEL;

`wget -O - https://www.redhat.com/security/data/oval/v2/RHEL7/rhel-7.oval.xml.bz2 | bzip2 --decompress > rhel-7.oval.xml`

For Ubuntu;

`wget -O - https://security-metadata.canonical.com/oval/com.ubuntu.$(lsb_release -cs).usn.oval.xml.bz2 | bzip2 --decompress > ubuntu-2004.oval.xml`

Let's scan

`oscap oval eval --report vulnerability.html rhel-7.oval.xml`

This will generate report.html, you can download and browse it.
