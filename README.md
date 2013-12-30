Open Data Element (ODE)
=======================
Karthik Ayyar <karthik@houseofkodai.in>

20131230

1. Introduction
===============
   An Open Data Element (ODE) is a self-defining structure of data that
   is **easy** to generate and **safe** to parse.

   ODE is a basic building block for file-formats and network-protocols.
   Network-protocols are essentially exchange of ODE between peers;
   This ODE eXchange is known as OX. A network of nodes communicating
   ODEs via. OX is known as Open Data Interface Network (ODIN).

   Although each element would seem restricted in size of upto 65533
   (2^16:65536-3) bytes, any length of data can be accomodated by
   encoding it as a sequence of fragmented ODEs. For example, a 2-byte
   fragmentation-header (id/count) extends the maximum size of each
   element to, almost 16MB (256 * 65531 = 16775936 bytes).

   ODE can be used recursively; an ODE can contain other ODEs.

   Defining file-formats and network-protocols is as simple as
   defining the ODE type-table. It is anticipated that application
   developers will publish ODE type-tables encouraging interoperability.

2. Definition
=============

[Size] [[Type] [Data]]
----------------------

### [Size]
   * 2 bytes
   * for UDP packets, refers to sequence number, as size is implicit
     intended for jitter-buffers to deal with out-of-order/delayed
     packets.

### [Type]
   * 1 byte
   * 1 bit of fragment-flag (highest-bit fragment-flag)
   * 7 bits of type (decimal values 0-127)

### [Data]
   * (size-1) bytes

note:
  * all bytes are Network byte order
  * for textual formats size and type can be specified as %%type\n
    enabling easy parsing/translation of %%type\n into
    3-byte [size][type]
  * human readable text formats can use \n as type-data seperator
    and characters between type and \n as a comment
  * human readable type definition is either printable character or
    \nnn numeric value of type (0-127)

3. 0.ODE: Reserved Type Table Definition
========================================
     Type Description
     ----------------
     58 : Type-table definition
          [* 0-or-more bytes space delimiter]
          [numeric-string ASCII value (<127) of Type]
          [1+ bytes space delimiter]
          [description]
          [2 byte \n\n new-line new-line description terminator]

      0   Block of Bytes
          [description]
          [1 byte \0 null terminator]
          [data bytes]
          useful for encapsulating files.

     42 * Group of elements
          [numeric-string number of elements]
          [1 byte space delimiter]
          [zero or more ODElements]
          multiplex multiple elements into a single element/packet

     46 . Pointer/Index to previous element
          [2-byte previous-element-offset]
          [(size-2) bytes unique-marker]
          random-file-seek: search for unique-marker and then
          traverse first to previous-element and then to other elements
          as required.

     33 ! Identity Element
          1-byte space delimited words
          [id] [status] [params...]
          ! karthik idle version x microphone camera tv speaker

     63 ? Request
          [reQuest id]
          [1 byte space delimiter]
          [command]
          [1 byte space delimiter]
          [params...]

     61 = Response-pending
          [reQuest id]
          [pending status descripton]

     43 + Response-success
          [reQuest id]
          [1 byte space delimiter]
          [response data]

     45 - Response-failure
          [reQuest id]
          [1 byte space delimiter]
          [error code numeric]
          [1 byte space delimiter]
          [error code description]

     62 > Response-redirect
          [reQuest id]
          [1 byte space delimiter]
          [redirect URL]
