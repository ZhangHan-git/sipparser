sipparser
=========

###About

sipparser is a high performce parser for Session Initiated
Protocol messages based on [sip_parser](https://bitbucket.org/sdr/sip_parser).  It provides a library for use in building 
SIP user agents or any program in go that needs to be able to
parse SIP messages.

###Installation

(the following steps assume that you have mercurial and go
installed already)
1. hg clone https://sdr@bitbucket.org/sdr/sip_parser
2. cd sip_parser
3. make clean && make && make install


Usage

The library has an easy to use interface.  

1. call sipparser.ParseMsg(msg string)
2. you'll get back a *SipMsg struct with the following:
    -- State is the last parsing state
    -- Error is an os.Error
    -- Msg is the raw msg
    -- CallingParty is a *CallingParty struct (see below)
    -- Body is the body of the message
    -- StartLine is the parsed StartLine (see below)
    -- Headers is a slice of *Headers (see below) and will
       only contain headers that do not get parsed 
    -- Accept is a *Accept struct (see below)
    -- AlertInfo is just the string of the Alert-Info hdr
    -- Allow is a slice of strings of the methods that are 
       allowed
    -- AllowEvents is a slice of strings of the supported
       event types
    -- ContentDisposition is a *ContentDisposition struct 
    -- ContentLength is the value of the Content-Length hdr
    -- ContentLengthInt is the int value of the ContentLength
    -- ContentType is the header value for the Content-Type hdr
    -- From is a *From struct (see below)
    -- MaxForwards is the hdr value for the Max-Forwards hdr
    -- MaxForwardsInt is the int value of the MaxForwards field
    -- Organization is the value for the Organization hdr
    -- To is a *From struct (see below)
    -- Contact is a *From struct (see below) 
       ** NOTE ** Contact is not parsed automatically.  You 
       have to call *SipMsg.ParseContact() to get this value.
    -- ContactVal is the raw value of the contact hdr
    -- CallId is the call-id for the message
    -- Cseq is a *Cseq struct (see below)
    -- Rack is a *Rack struct (see below)
    -- Reason is a *Reason struct
    -- Rseq is the value of the RSeq hdr
    -- RseqInt is the int value of the Rseq
    -- RecordRoute is a slice of *URI's structs (see below)
    -- Route is a slice of *URI's structs (see below) 
    -- Via is a slice of *Via structs (see below)
    -- Require is a slice of the required extensions (string
       values) from the Require hdr
    -- Supported is a slice of the supported extensions (string
       values) from the Supported hdr
    -- Privacy is the value of the Privacy hdr
    -- ProxyRequire is a slice of strings from the 
       Proxy-Require hdr
    -- RemotePartyIdVal is the value from the Remote-Party-Id hdr
    -- RemotePartyId is the RemotePartyId struct 
       ** NOTE ** In order to actually get this value you have to
       call *SipMsg.ParseRemotePartyId()
    -- PAssertedIdVal is the value from the P-Asserted-Identity hdr
    -- PAssertedId is the *PAssertedId struct
       ** NOTE ** In order to actually get this value you have to 
       call *SipMsg.ParsePAssertedId()
    -- Unsupported is a slice of the unsupported extensions from
       the Unsupported hdr
    -- UserAgent is the value of the User-Agent hdr
    -- Server is the value of the Server hdr
    -- Subject is the value of the Subject hdr
    -- Warning is a *Warning struct (see below)

Import Types 

The following types are also part of the parsed SIP message. 

Accept is a struct with the following fields:
-- Val is the raw value
-- Params is a slice of AcceptParam

CallingPartyInfo is a struct of calling party information.  
This is populated into the *SipMsg.CallingParty field when 
the method GetCallingParty on the *SipMsg.  See below for 
details of that method.
CallingPartyInfo has the following fields:
-- Name the name
-- Number the number
-- Anonymous a bool to see if this should be anonymous or not


ContentDisposition is a struct with the following fields:
-- Val is the raw value
-- DispType is the display type
-- Params is a slice of *Param (see below)

Cseq is a struct with the following fields:
-- Val is the raw value
-- Method is the SIP method
-- Digit is the digit (although in a string format)

From is an important type that is used as a representation of
the parsed hdr values for the From, To, and Contact headers.
The From struct has the following fields:
-- Error is an os.Error
-- Val is the raw value
-- Name is the name value from the hdr
-- Tag is the value of the tag=$someval parameter
-- URI is the *URI 
-- Params is a slice of *Param (see below)

Param is a struct with the following fields:
-- Param is the parameter 
-- Val is the value of the parameter (if any ...)

PAssertedId is a struct with the following fields:
-- Error is an os.Error
-- Val is the raw value
-- Name is the name from the header
-- URI is the *URI
-- Params is a slice of *Param

Rack is a struct with the following fields:
-- Val is the raw value
-- RseqVal is the value of the rseq
-- CseqVal is the value of the cseq
-- CseqMethod is the value of the cseq method

Reason is a struct with the following fields:
-- Val is the raw value
-- Proto is the protocol
-- Cause is the cause code
-- Text is the text

RemotePartyId is a struct with the following fields:
-- Error is an os.Error
-- Val is the raw value of the hdr
-- Name is the name from the header
-- URI is the *URI
-- Party is the party parameter
-- Screen is the screen parameter 
-- Privacy is the privacy parameeter
-- Params is a slice of *Param

StartLine is a struct with the following fields:
-- Error is an os.Error
-- Val is the raw value
-- Type is the type of startline (i.e. request or response)
-- Method is the method (if request)
-- URI is the *URI (if request) 
-- Resp is the response code (i.e. 183)
-- RespText is the response text (i.e. "Session Progress")
-- Proto is the protocol (should be "SIP")
-- Version is the version (should be "2.0")

URI is an important struct that is used in many places 
through a *SipMsg from the From header to the StartLine.
It has the following fields:
-- Error is an os.Error
-- Scheme is the scheme (i.e. "sip", "sips", "tel")
-- Raw is the raw value of the uri 
-- UserInfo is everything before the "@" char if anything 
-- User is the user (i.e. "bob")
-- UserPassword is the password (if any) 
-- HostInfo is everything between the "@" char and any parameters
-- Host is the host
-- Port is the port
-- UriParams is a slice of *Param
-- Secure is a bool indicating if communication is secure 

Via is an important part of the *SipMsg.  It is a fundamental
basis on which to build route-sets and do call matching.  It
has the following structs:
-- State is the parser state
-- Error is an os.Error
-- Via is the raw value
-- Proto is the protocol (i.e. "SIP")
-- Version is the version (i.e. "2.0")
-- Transport is the transport method (i.e. "UDP")
-- SentBy is a host:port combination 
-- Branch is the branch parameter
-- Params is a slice of *Param

Import Methods on the *SipMsg

GetCallingParty

GetCallingParty is a method that can be called with one of 
the following:
-- rpid (abbr for remote-party-id)
-- paid (abbr for party-associated-identity)
If either of the above parameters are passed to the method
then it will use one of the matching hdrs to obtain the info.
If anything else is passed then it will pull the CallingPartyInfo
from the from header.

GetRURIParamBool

GetRURIParamBool returns true or false to see if a parameter
is present in the request URI

GetRURIParamVal 

GetRURIParamVal returns the actual value of the parameter if
the parameter is present in the request URI

###SIP grammar from RFC3261
```
   alphanum  =  ALPHA / DIGIT

   reserved    =  ";" / "/" / "?" / ":" / "@" / "&" / "=" / "+"
                  / "$" / ","
   unreserved  =  alphanum / mark
   mark        =  "-" / "_" / "." / "!" / "~" / "*" / "'"
                  / "(" / ")"
   escaped     =  "%" HEXDIG HEXDIG

   LWS  =  [*WSP CRLF] 1*WSP ; linear whitespace
   SWS  =  [LWS] ; sep whitespace

   HCOLON  =  *( SP / HTAB ) ":" SWS

   TEXT-UTF8-TRIM  =  1*TEXT-UTF8char *(*LWS TEXT-UTF8char)
   TEXT-UTF8char   =  %x21-7E / UTF8-NONASCII
   UTF8-NONASCII   =  %xC0-DF 1UTF8-CONT
                   /  %xE0-EF 2UTF8-CONT
                   /  %xF0-F7 3UTF8-CONT
                   /  %xF8-Fb 4UTF8-CONT
                   /  %xFC-FD 5UTF8-CONT
   UTF8-CONT       =  %x80-BF

   LHEX  =  DIGIT / %x61-66 ;lowercase a-f

   token       =  1*(alphanum / "-" / "." / "!" / "%" / "*"
                  / "_" / "+" / "`" / "'" / "~" )
   separators  =  "(" / ")" / "<" / ">" / "@" /
                  "," / ";" / ":" / "\" / DQUOTE /
                  "/" / "[" / "]" / "?" / "=" /
                  "{" / "}" / SP / HTAB
   word        =  1*(alphanum / "-" / "." / "!" / "%" / "*" /
                  "_" / "+" / "`" / "'" / "~" /
                  "(" / ")" / "<" / ">" /
                  ":" / "\" / DQUOTE /
                  "/" / "[" / "]" / "?" /
                  "{" / "}" )

   STAR    =  SWS "*" SWS ; asterisk
   SLASH   =  SWS "/" SWS ; slash
   EQUAL   =  SWS "=" SWS ; equal
   LPAREN  =  SWS "(" SWS ; left parenthesis
   RPAREN  =  SWS ")" SWS ; right parenthesis
   RAQUOT  =  ">" SWS ; right angle quote
   LAQUOT  =  SWS "<"; left angle quote
   COMMA   =  SWS "," SWS ; comma
   SEMI    =  SWS ";" SWS ; semicolon
   COLON   =  SWS ":" SWS ; colon
   LDQUOT  =  SWS DQUOTE; open double quotation mark
   RDQUOT  =  DQUOTE SWS ; close double quotation mark

   comment  =  LPAREN *(ctext / quoted-pair / comment) RPAREN
   ctext    =  %x21-27 / %x2A-5B / %x5D-7E / UTF8-NONASCII
               / LWS

   quoted-string  =  SWS DQUOTE *(qdtext / quoted-pair ) DQUOTE
   qdtext         =  LWS / %x21 / %x23-5B / %x5D-7E
                     / UTF8-NONASCII

   quoted-pair  =  "\" (%x00-09 / %x0B-0C
                   / %x0E-7F)

   SIP-URI          =  "sip:" [ userinfo ] hostport
                       uri-parameters [ headers ]
   SIPS-URI         =  "sips:" [ userinfo ] hostport
                       uri-parameters [ headers ]
   userinfo         =  ( user / telephone-subscriber ) [ ":" password ] "@"
   user             =  1*( unreserved / escaped / user-unreserved )
   user-unreserved  =  "&" / "=" / "+" / "$" / "," / ";" / "?" / "/"
   password         =  *( unreserved / escaped /
                       "&" / "=" / "+" / "$" / "," )
   hostport         =  host [ ":" port ]
   host             =  hostname / IPv4address / IPv6reference
   hostname         =  *( domainlabel "." ) toplabel [ "." ]
   domainlabel      =  alphanum
                       / alphanum *( alphanum / "-" ) alphanum
   toplabel         =  ALPHA / ALPHA *( alphanum / "-" ) alphanum
   IPv4address    =  1*3DIGIT "." 1*3DIGIT "." 1*3DIGIT "." 1*3DIGIT
   IPv6reference  =  "[" IPv6address "]"
   IPv6address    =  hexpart [ ":" IPv4address ]
   hexpart        =  hexseq / hexseq "::" [ hexseq ] / "::" [ hexseq ]
   hexseq         =  hex4 *( ":" hex4)
   hex4           =  1*4HEXDIG
   port           =  1*DIGIT

   uri-parameters    =  *( ";" uri-parameter)
   uri-parameter     =  transport-param / user-param / method-param
                        / ttl-param / maddr-param / lr-param / other-param
   transport-param   =  "transport="
                        ( "udp" / "tcp" / "sctp" / "tls"
                        / other-transport)
   other-transport   =  token
   user-param        =  "user=" ( "phone" / "ip" / other-user)
   other-user        =  token
   method-param      =  "method=" Method
   ttl-param         =  "ttl=" ttl
   maddr-param       =  "maddr=" host
   lr-param          =  "lr"
   other-param       =  pname [ "=" pvalue ]
   pname             =  1*paramchar
   pvalue            =  1*paramchar
   paramchar         =  param-unreserved / unreserved / escaped
   param-unreserved  =  "[" / "]" / "/" / ":" / "&" / "+" / "$"

   headers         =  "?" header *( "&" header )
   header          =  hname "=" hvalue
   hname           =  1*( hnv-unreserved / unreserved / escaped )
   hvalue          =  *( hnv-unreserved / unreserved / escaped )
   hnv-unreserved  =  "[" / "]" / "/" / "?" / ":" / "+" / "$"

   SIP-message    =  Request / Response
   Request        =  Request-Line
                     *( message-header )
                     CRLF
                     [ message-body ]
   Request-Line   =  Method SP Request-URI SP SIP-Version CRLF
   Request-URI    =  SIP-URI / SIPS-URI / absoluteURI
   absoluteURI    =  scheme ":" ( hier-part / opaque-part )
   hier-part      =  ( net-path / abs-path ) [ "?" query ]
   net-path       =  "//" authority [ abs-path ]
   abs-path       =  "/" path-segments
   opaque-part    =  uric-no-slash *uric
   uric           =  reserved / unreserved / escaped
   uric-no-slash  =  unreserved / escaped / ";" / "?" / ":" / "@"
                     / "&" / "=" / "+" / "$" / ","
   path-segments  =  segment *( "/" segment )
   segment        =  *pchar *( ";" param )
   param          =  *pchar
   pchar          =  unreserved / escaped /
                     ":" / "@" / "&" / "=" / "+" / "$" / ","
   scheme         =  ALPHA *( ALPHA / DIGIT / "+" / "-" / "." )
   authority      =  srvr / reg-name
   srvr           =  [ [ userinfo "@" ] hostport ]
   reg-name       =  1*( unreserved / escaped / "$" / ","
                     / ";" / ":" / "@" / "&" / "=" / "+" )
   query          =  *uric
   SIP-Version    =  "SIP" "/" 1*DIGIT "." 1*DIGIT

   message-header  =  (Accept
                   /  Accept-Encoding
                   /  Accept-Language
                   /  Alert-Info
                   /  Allow
                   /  Authentication-Info
                   /  Authorization
                   /  Call-ID
                   /  Call-Info
                   /  Contact
                   /  Content-Disposition
                   /  Content-Encoding
                   /  Content-Language
                   /  Content-Length
                   /  Content-Type
                   /  CSeq
                   /  Date
                   /  Error-Info
                   /  Expires
                   /  From
                   /  In-Reply-To
                   /  Max-Forwards
                   /  MIME-Version
                   /  Min-Expires
                   /  Organization
                   /  Priority
                   /  Proxy-Authenticate
                   /  Proxy-Authorization
                   /  Proxy-Require
                   /  Record-Route
                   /  Reply-To
                   /  Require
                   /  Retry-After
                   /  Route
                   /  Server
                   /  Subject
                   /  Supported
                   /  Timestamp
                   /  To
                   /  Unsupported
                   /  User-Agent
                   /  Via
                   /  Warning
                   /  WWW-Authenticate
                   /  extension-header) CRLF

   INVITEm           =  %x49.4E.56.49.54.45 ; INVITE in caps
   ACKm              =  %x41.43.4B ; ACK in caps
   OPTIONSm          =  %x4F.50.54.49.4F.4E.53 ; OPTIONS in caps
   BYEm              =  %x42.59.45 ; BYE in caps
   CANCELm           =  %x43.41.4E.43.45.4C ; CANCEL in caps
   REGISTERm         =  %x52.45.47.49.53.54.45.52 ; REGISTER in caps
   Method            =  INVITEm / ACKm / OPTIONSm / BYEm
                        / CANCELm / REGISTERm
                        / extension-method
   extension-method  =  token
   Response          =  Status-Line
                        *( message-header )
                        CRLF
                        [ message-body ]

   Status-Line     =  SIP-Version SP Status-Code SP Reason-Phrase CRLF
   Status-Code     =  Informational
                  /   Redirection
                  /   Success
                  /   Client-Error
                  /   Server-Error
                  /   Global-Failure
                  /   extension-code
   extension-code  =  3DIGIT
   Reason-Phrase   =  *(reserved / unreserved / escaped
                      / UTF8-NONASCII / UTF8-CONT / SP / HTAB)

   Informational  =  "100"  ;  Trying
                 /   "180"  ;  Ringing
                 /   "181"  ;  Call Is Being Forwarded
                 /   "182"  ;  Queued
                 /   "183"  ;  Session Progress

   Success  =  "200"  ;  OK

   Redirection  =  "300"  ;  Multiple Choices
               /   "301"  ;  Moved Permanently
               /   "302"  ;  Moved Temporarily
               /   "305"  ;  Use Proxy
               /   "380"  ;  Alternative Service

   Client-Error  =  "400"  ;  Bad Request
                /   "401"  ;  Unauthorized
                /   "402"  ;  Payment Required
                /   "403"  ;  Forbidden
                /   "404"  ;  Not Found
                /   "405"  ;  Method Not Allowed
                /   "406"  ;  Not Acceptable
                /   "407"  ;  Proxy Authentication Required
                /   "408"  ;  Request Timeout
                /   "410"  ;  Gone
                /   "413"  ;  Request Entity Too Large
                /   "414"  ;  Request-URI Too Large
                /   "415"  ;  Unsupported Media Type
                /   "416"  ;  Unsupported URI Scheme
                /   "420"  ;  Bad Extension
                /   "421"  ;  Extension Required
                /   "423"  ;  Interval Too Brief
                /   "480"  ;  Temporarily not available
                /   "481"  ;  Call Leg/Transaction Does Not Exist
                /   "482"  ;  Loop Detected
                /   "483"  ;  Too Many Hops
                /   "484"  ;  Address Incomplete
                /   "485"  ;  Ambiguous
                /   "486"  ;  Busy Here
                /   "487"  ;  Request Terminated
                /   "488"  ;  Not Acceptable Here
                /   "491"  ;  Request Pending
                /   "493"  ;  Undecipherable

   Server-Error  =  "500"  ;  Internal Server Error
                /   "501"  ;  Not Implemented
                /   "502"  ;  Bad Gateway
                /   "503"  ;  Service Unavailable
                /   "504"  ;  Server Time-out
                /   "505"  ;  SIP Version not supported
                /   "513"  ;  Message Too Large

   Global-Failure  =  "600"  ;  Busy Everywhere
                  /   "603"  ;  Decline
                  /   "604"  ;  Does not exist anywhere
                  /   "606"  ;  Not Acceptable

   Accept         =  "Accept" HCOLON
                      [ accept-range *(COMMA accept-range) ]
   accept-range   =  media-range *(SEMI accept-param)
   ; Why not SLASH ? //PPe
   media-range    =  ( "*" "/" "*"
                     / ( m-type SLASH "*" )
                     / ( m-type SLASH m-subtype )
                     ) *( SEMI m-parameter )
   accept-param   =  ("q" EQUAL qvalue) / generic-param
   qvalue         =  ( "0" [ "." 0*3DIGIT ] )
                     / ( "1" [ "." 0*3("0") ] )
   generic-param  =  token [ EQUAL gen-value ]
   gen-value      =  token / host / quoted-string

   Accept-Encoding  =  "Accept-Encoding" HCOLON
                        [ encoding *(COMMA encoding) ]
   encoding         =  codings *(SEMI accept-param)
   codings          =  content-coding / "*"
   content-coding   =  token

   Accept-Language  =  "Accept-Language" HCOLON
                        [ language *(COMMA language) ]
   language         =  language-range *(SEMI accept-param)
   language-range   =  ( ( 1*8ALPHA *( "-" 1*8ALPHA ) ) / "*" )

   Alert-Info   =  "Alert-Info" HCOLON alert-param *(COMMA alert-param)
   alert-param  =  LAQUOT absoluteURI RAQUOT *( SEMI generic-param )

   Allow  =  "Allow" HCOLON [Method *(COMMA Method)]

   Authorization     =  "Authorization" HCOLON credentials
   credentials       =  ("Digest" LWS digest-response)
                        / other-response
   digest-response   =  dig-resp *(COMMA dig-resp)
   dig-resp          =  username / realm / nonce / digest-uri
                         / dresponse / algorithm / cnonce
                         / opaque / message-qop
                         / nonce-count / auth-param
   username          =  "username" EQUAL username-value
   username-value    =  quoted-string
   digest-uri        =  "uri" EQUAL LDQUOT digest-uri-value RDQUOT
   digest-uri-value  =  rquest-uri ; Equal to request-uri as specified
                        by HTTP/1.1
   message-qop       =  "qop" EQUAL qop-value
   cnonce            =  "cnonce" EQUAL cnonce-value
   cnonce-value      =  nonce-value
   nonce-count       =  "nc" EQUAL nc-value
   nc-value          =  8LHEX
   dresponse         =  "response" EQUAL request-digest
   request-digest    =  LDQUOT 32LHEX RDQUOT
   auth-param        =  auth-param-name EQUAL
                        ( token / quoted-string )
   auth-param-name   =  token
   other-response    =  auth-scheme LWS auth-param
                        *(COMMA auth-param)
   auth-scheme       =  token

   Authentication-Info  =  "Authentication-Info" HCOLON ainfo
                           *(COMMA ainfo)
   ainfo                =  nextnonce / message-qop
                            / response-auth / cnonce
                            / nonce-count
   nextnonce            =  "nextnonce" EQUAL nonce-value
   response-auth        =  "rspauth" EQUAL response-digest
   response-digest      =  LDQUOT *LHEX RDQUOT

   Call-ID  =  ( "Call-ID" / "i" ) HCOLON callid
   callid   =  word [ "@" word ]

   Call-Info   =  "Call-Info" HCOLON info *(COMMA info)
   info        =  LAQUOT absoluteURI RAQUOT *( SEMI info-param)
   info-param  =  ( "purpose" EQUAL ( "icon" / "info"
                  / "card" / token ) ) / generic-param

   Contact        =  ("Contact" / "m" ) HCOLON
                     ( STAR / (contact-param *(COMMA contact-param)))
   contact-param  =  (name-addr / addr-spec) *(SEMI contact-params)
   name-addr      =  [ display-name ] LAQUOT addr-spec RAQUOT
   addr-spec      =  SIP-URI / SIPS-URI / absoluteURI
   display-name   =  *(token LWS)/ quoted-string

   contact-params     =  c-p-q / c-p-expires
                         / contact-extension
   c-p-q              =  "q" EQUAL qvalue
   c-p-expires        =  "expires" EQUAL delta-seconds
   contact-extension  =  generic-param
   delta-seconds      =  1*DIGIT

   Content-Disposition   =  "Content-Disposition" HCOLON
                            disp-type *( SEMI disp-param )
   disp-type             =  "render" / "session" / "icon" / "alert"
                            / disp-extension-token
   disp-param            =  handling-param / generic-param
   handling-param        =  "handling" EQUAL
                            ( "optional" / "required"
                            / other-handling )
   other-handling        =  token
   disp-extension-token  =  token

   Content-Encoding  =  ( "Content-Encoding" / "e" ) HCOLON
                        content-coding *(COMMA content-coding)

   Content-Language  =  "Content-Language" HCOLON
                        language-tag *(COMMA language-tag)
   language-tag      =  primary-tag *( "-" subtag )
   primary-tag       =  1*8ALPHA
   subtag            =  1*8ALPHA

   Content-Length  =  ( "Content-Length" / "l" ) HCOLON 1*DIGIT
   Content-Type     =  ( "Content-Type" / "c" ) HCOLON media-type
   media-type       =  m-type SLASH m-subtype *(SEMI m-parameter)
   m-type           =  discrete-type / composite-type
   discrete-type    =  "text" / "image" / "audio" / "video"
                       / "application" / extension-token
   composite-type   =  "message" / "multipart" / extension-token
   extension-token  =  ietf-token / x-token
   ietf-token       =  token
   x-token          =  "x-" token
   m-subtype        =  extension-token / iana-token
   iana-token       =  token
   m-parameter      =  m-attribute EQUAL m-value
   m-attribute      =  token
   m-value          =  token / quoted-string

   CSeq  =  "CSeq" HCOLON 1*DIGIT LWS Method

   Date          =  "Date" HCOLON SIP-date
   SIP-date      =  rfc1123-date
   rfc1123-date  =  wkday "," SP date1 SP time SP "GMT"
   date1         =  2DIGIT SP month SP 4DIGIT
                    ; day month year (e.g., 02 Jun 1982)
   time          =  2DIGIT ":" 2DIGIT ":" 2DIGIT
                    ; 00:00:00 - 23:59:59
   wkday         =  "Mon" / "Tue" / "Wed"
                    / "Thu" / "Fri" / "Sat" / "Sun"
   month         =  "Jan" / "Feb" / "Mar" / "Apr"
                    / "May" / "Jun" / "Jul" / "Aug"
                    / "Sep" / "Oct" / "Nov" / "Dec"

   Error-Info  =  "Error-Info" HCOLON error-uri *(COMMA error-uri)
   error-uri   =  LAQUOT absoluteURI RAQUOT *( SEMI generic-param )

   Expires     =  "Expires" HCOLON delta-seconds
   From        =  ( "From" / "f" ) HCOLON from-spec
   from-spec   =  ( name-addr / addr-spec )
                  *( SEMI from-param )
   from-param  =  tag-param / generic-param
   tag-param   =  "tag" EQUAL token

   In-Reply-To  =  "In-Reply-To" HCOLON callid *(COMMA callid)

   Max-Forwards  =  "Max-Forwards" HCOLON 1*DIGIT

   MIME-Version  =  "MIME-Version" HCOLON 1*DIGIT "." 1*DIGIT

   Min-Expires  =  "Min-Expires" HCOLON delta-seconds

   Organization  =  "Organization" HCOLON [TEXT-UTF8-TRIM]

   Priority        =  "Priority" HCOLON priority-value
   priority-value  =  "emergency" / "urgent" / "normal"
                      / "non-urgent" / other-priority
   other-priority  =  token

   Proxy-Authenticate  =  "Proxy-Authenticate" HCOLON challenge
   challenge           =  ("Digest" LWS digest-cln *(COMMA digest-cln))
                          / other-challenge
   other-challenge     =  auth-scheme LWS auth-param
                          *(COMMA auth-param)
   digest-cln          =  realm / domain / nonce
                           / opaque / stale / algorithm
                           / qop-options / auth-param
   realm               =  "realm" EQUAL realm-value
   realm-value         =  quoted-string
   domain              =  "domain" EQUAL LDQUOT URI
                          *( 1*SP URI ) RDQUOT
   URI                 =  absoluteURI / abs-path
   nonce               =  "nonce" EQUAL nonce-value
   nonce-value         =  quoted-string
   opaque              =  "opaque" EQUAL quoted-string
   stale               =  "stale" EQUAL ( "true" / "false" )
   algorithm           =  "algorithm" EQUAL ( "MD5" / "MD5-sess"
                          / token )
   qop-options         =  "qop" EQUAL LDQUOT qop-value
                          *("," qop-value) RDQUOT
   qop-value           =  "auth" / "auth-int" / token

   Proxy-Authorization  =  "Proxy-Authorization" HCOLON credentials

   Proxy-Require  =  "Proxy-Require" HCOLON option-tag
                     *(COMMA option-tag)
   option-tag     =  token

   Record-Route  =  "Record-Route" HCOLON rec-route *(COMMA rec-route)
   rec-route     =  name-addr *( SEMI rr-param )
   rr-param      =  generic-param

   Reply-To      =  "Reply-To" HCOLON rplyto-spec
   rplyto-spec   =  ( name-addr / addr-spec )
                    *( SEMI rplyto-param )
   rplyto-param  =  generic-param
   Require       =  "Require" HCOLON option-tag *(COMMA option-tag)

   Retry-After  =  "Retry-After" HCOLON delta-seconds
                   [ comment ] *( SEMI retry-param )

   retry-param  =  ("duration" EQUAL delta-seconds)
                   / generic-param

   Route        =  "Route" HCOLON route-param *(COMMA route-param)
   route-param  =  name-addr *( SEMI rr-param )

   Server           =  "Server" HCOLON server-val *(LWS server-val)
   server-val       =  product / comment
   product          =  token [SLASH product-version]
   product-version  =  token

   Subject  =  ( "Subject" / "s" ) HCOLON [TEXT-UTF8-TRIM]

   Supported  =  ( "Supported" / "k" ) HCOLON
                 [option-tag *(COMMA option-tag)]

   Timestamp  =  "Timestamp" HCOLON 1*(DIGIT)
                  [ "." *(DIGIT) ] [ LWS delay ]
   delay      =  *(DIGIT) [ "." *(DIGIT) ]

   To        =  ( "To" / "t" ) HCOLON ( name-addr
                / addr-spec ) *( SEMI to-param )
   to-param  =  tag-param / generic-param

   Unsupported  =  "Unsupported" HCOLON option-tag *(COMMA option-tag)
   User-Agent  =  "User-Agent" HCOLON server-val *(LWS server-val)

   Via               =  ( "Via" / "v" ) HCOLON via-parm *(COMMA via-parm)
   via-parm          =  sent-protocol LWS sent-by *( SEMI via-params )
   via-params        =  via-ttl / via-maddr
                        / via-received / via-branch
                        / via-extension
   via-ttl           =  "ttl" EQUAL ttl
   via-maddr         =  "maddr" EQUAL host
   via-received      =  "received" EQUAL (IPv4address / IPv6address)
   via-branch        =  "branch" EQUAL token
   via-extension     =  generic-param
   sent-protocol     =  protocol-name SLASH protocol-version
                        SLASH transport
   protocol-name     =  "SIP" / token
   protocol-version  =  token
   transport         =  "UDP" / "TCP" / "TLS" / "SCTP"
                        / other-transport
   sent-by           =  host [ COLON port ]
   ttl               =  1*3DIGIT ; 0 to 255

   Warning        =  "Warning" HCOLON warning-value *(COMMA warning-value)
   warning-value  =  warn-code SP warn-agent SP warn-text
   warn-code      =  3DIGIT
   warn-agent     =  hostport / pseudonym
                     ;  the name or pseudonym of the server adding
                     ;  the Warning header, for use in debugging
   warn-text      =  quoted-string
   pseudonym      =  token

   WWW-Authenticate  =  "WWW-Authenticate" HCOLON challenge

   extension-header  =  header-name HCOLON header-value
   header-name       =  token
   header-value      =  *(TEXT-UTF8char / UTF8-CONT / LWS)
   message-body  =  *OCTET
```