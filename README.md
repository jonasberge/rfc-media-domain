# RFCs for offering Source Information with Desktop Media

> WORK IN PROGRESS
>
> This document and the documents in the root of this repository
are a work in progress.
If you are an individual who works on
the Windows operating system,
the Macintosh operating system,
the Linux kernel or any Linux distribution,
Chromium, Firefox or any other browser,
feel free to reach out by opening an issue in this repository,
by opening a pull request,
or by sending an e-mail to the author.

## Abstract

The documents in the root of this repository
outline how system APIs that already exist
to retrieve metadata information about media playback on desktop computers
could be extended to additionally supply information
about which internet service is used as the underlying source of the media,
identified by its internet domain name.
The purpose of this is to give clients of those system APIs the opportunity
to discern where media is coming from exactly,
when it is impossible to tell by the name of the application
that is playing the media alone.
More specifically, it would be possible to tell which online service is used,
for media that is being played inside an internet browser,
which is otherwise impossible with the current state of available system APIs.

This document (README.md) only acts as an overview
for suggested changes to existing system APIs and applications,
which are further detailed in the individual RFC documents in this repository.
For implementation details, please consult these individual documents.

## Introduction

The system APIs of modern operating systems offer programmable interfaces
to both get information about currently playing media on a desktop computer,
and to communicate to the operating system
which media the application that interfaces with the system API
is currently playing.
With these APIs applications can retrieve information
about what media is being played on the system,
which is useful for many different purposes
that are already established and won't be reiterated in detail.

## Identifying the Media Source Application

With all system APIs it is already possible
to retrieve the name of the application
that is playing a specific piece of media.

On the Windows operating systems this is either the executable name
or an "Application User Model ID"
[[AppUserModelID](https://learn.microsoft.com/en-us/windows/win32/shell/appids)].

On the Macintosh operating system this is the bundle identifier
[[CFBundleIdentifier](https://developer.apple.com/documentation/bundleresources/information_property_list/cfbundleidentifier)].

On Linux operating systems this is application name
*[citation needed]*.

## Identifying the real Media Source

The problem arises when the application name does not constitute
to identifying the underlying source of the media anymore
because the application itself is so generic in its nature,
that it can play media from multiple different sources
or any source in general.
The best example of this are internet browsers,
which can be used to visit online websites
that can and will play anything a user of that browser wishes for.
The name of the application does not suffice anymore,
to be able to tell the which entity or service the media belongs to,
whether it is a local file or if it is being streamed from a server.
It could be from a music streaming service,
it could be a video from a video-on-demand service
or it could really just be a generic local audio file.

MPRIS [[MPRISv2](https://specifications.freedesktop.org/mpris-spec/2.2/)]
already provides a means of identifying a "track",
a piece of media, in the context of its media player (the application)
with the "mpris:trackid" field
[[MPRISv2:Metadata](https://www.freedesktop.org/wiki/Specifications/mpris-spec/metadata/#mpris:trackid)].
While this does not help with identifying the source of the media,
the implementation of it is similar to what is needed
to identify the media source.
There should be some identifier,
which helps in discerning where media is coming from.

## Using Domain Names to identify the Media Source

> This is a draft and might see changes in the future.

If an application uses a *single source* it is in most cases either
a media player for local audio files (offline players)
or a desktop client for a streaming service
*[citation needed]*.
In these cases the application name is sufficient,
to identify where the media is coming from.

With applications that can play media from multiple different sources,
this usually means that these *different sources* are *web services*
(as is already the case with desktop clients for streaming services,
which also make use of web services).

The idea is that system APIs of desktop computers
should not only supply the name of the application,
but, in addition to that, the name of the web service
from which the media is retrieved.

Since using the concept of a "web service" is rather specific,
it makes sense to generalize the concept
and find an identifier that is more generic, while still being unique.
The first thing that comes to mind,
when it's about globally identifying something on the internet,
is internet domains.

Internet domains are widely used, well-supported, globally unique
and each service provider or vendor of a major application (usually) has one.
Internet domains are used in the following contexts
to allow for unique identification of entities:

- Java package names use domains in reverse-DNS format *[citation needed]*.
- Applications for the Macintosh operating system use bundle identifiers,
  which are domains in reverse-DNS format
  [[CFBundleIdentifier](https://developer.apple.com/documentation/bundleresources/information_property_list/cfbundleidentifier)].
- Web services, by nature, are accessible over the internet,
  which is where internet domains are at home.
  Usually, every notable web service also has a domain name
  with which it can be identified.

An application would, in addition to all media metadata,
supply a domain name that identifies the service that was used
to retrieve the media, in case any service was used as a source.
Specific implementation details are discussed in the individual RFC documents.

## Examples

To get a better understanding of the material,
here are some examples where the described type of identifier can be useful.
For the sake of brevity, everything that identifies a particular piece of media
is summarized under the "Media" column,
which might include the title of the media and the artist.

In all examples the following columns are used:

- **Application Name**: The name of the application that is playing the media.
- **Application Identifier**: The identifier for the application,
  which is unique for every operating system.
  With these examples Windows executable names are used.
- **Media**: A particular piece of media that is played by the application,
  formatted as "Title" or "Title - Artist".
  In reality there can be much more information about the media
  that is supplied by the operating system,
  for the sake of brevity this is summarized here as a short string.
- **Media Source Identifier**: The proposed identifier
  for identifying the underlying source of the media
  that is played by the application.
  In this case domains are used in reverse-DNS format.
- **Vendor URL**: A URL to the website of
  the application vendor or service provider,
  to show the relationship between the *Media Source Identifier*
  and the internet domain of the application or service provider.

### Example 1: Desktop Applications

| Application Name | Application Identifier | Media | Media Source Identifier | Vendor URL |
|-|-|-|-|-|
| Spotify | Spotify.exe | Allegro - Bach | `com.spotify` (optional) | https://spotify.com |
| TIDAL | TIDAL.exe | Fur Elise - Ludwig van Beethoven | `com.tidal` (optional)  | https://tidal.com |
| foobar2000 | foobar2000.exe | Allegro - Bach | `org.foobar2000` (optional)  | https://www.foobar2000.org |

Note that Media Source Identifiers are optional here.
Application developers do not have to choose to supply this identifier,
as it is already apparent from the application name and application identifier
where the media is originating from.
These examples are here to show that application identifiers *can be*
sufficient to help identify where media is coming from.
Where the Media Source Identifier really shines is the next example,
which shows where the application identifier itself is not sufficient
to identify where the media is coming from.

### Example 2: Multimedia Desktop Applications

In these examples only internet browsers are used.

Additional columns:

- **Website URL**: The URL of the website which is playing the media
  inside the browser.

| Application Name | Application Identifier | Media | Media Source Identifier | Website URL | Vendor URL |
|-|-|-|-|-|-|
| Firefox | firefox.exe | Allegro - Bach | `com.spotify` | https://open.spotify.com | https://www.mozilla.org |
| Google Chrome | chrome.exe | Allegro - Bach | `com.spotify` | https://open.spotify.com | https://www.google.com |
| Google Chrome | chrome.exe | Fur Elise - Ludwig van Beethoven | `com.tidal` | https://listen.tidal.com | https://www.google.com |

As we can see, we are still able to identify Spotify and TIDAL
as the underlying source for the media that is being played inside a browser.
The application name or application identifier itself
does not communicate this information,
but when the browser supplies the Media Source Identifier,
this information becomes available to us.

## Deriving the Media Source Identifier

Desktop applications as described in example 1
can simply use their own domain names.

Multimedia desktop applications as described in example 2
need some way to derive the Media Source Identifier from its content.

### Web Browsers

Web browsers may derive Media Source Identifiers from the website domain name
that is playing the media.
If media is e.g. played on `www.example.com`,
the Media Source Identifier could either be `com.example.www`
or `com.example`.
The exact details of which should or could be preferred
is outlined in the individual RFC documents.

### Other Multimedia Applications

Multimedia applications that work in a similar fashion to web browsers,
i.e. they retrieve media from an online service that has an internet domain,
should use the same logic as web browsers
to derive the Media Source Identifier.

If a multimedia application does not get media from an online service,
the Media Source Identifier should be empty.

## Request for Comments

The suggested changes in this repository heavily rely on the cooperation
with other individuals,
namely those who have an impact
on the design of system APIs for desktop operating systems,
which provide media playback information.
If you read this, you are encouraged to contribute,
by opening an issue, making a pull request
or forwarding a link to this repository to others.

## Individual RFC Documents

- [RFC-01](./rfc-01-linux-mpris.md): Extending Linux MPRIS to support Media Source Identifiers
- [RFC-02](./rfc-02-windows-winrt.md): Extending Windows WinRT to support Media Source Identifiers
- [RFC-03](./rfc-03-macos-mediaremote.md): Extending Macintosh's MediaRemote Framework to support Media Source Identifiers
- [RFC-04](./rfc-04-web-browsers.md): Extending web browsers to supply Media Source Identifiers

## References

[AppUserModelID]  
Windows App Development  
Application User Model IDs (AppUserModelIDs)  
https://learn.microsoft.com/en-us/windows/win32/shell/appids

[CFBundleIdentifier]  
Apple Developer  
CFBundleIdentifier  
https://developer.apple.com/documentation/bundleresources/information_property_list/cfbundleidentifier

[MPRISv2]  
Freedesktop.org Specifications  
MPRIS D-Bus Interface Specification  
https://specifications.freedesktop.org/mpris-spec/2.2/

[MPRISv2:Metadata]  
Freedesktop.org Specifications  
MPRIS v2 metadata guidelines  
https://www.freedesktop.org/wiki/Specifications/mpris-spec/metadata

## Copyright Notice

Copyright (c) 2024 Jonas van den Berg  
All rights reserved.

If you need a license for any of the information in this repository,
please contact me.
