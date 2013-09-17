Open Data Element (ODE)
=======================
Karthik Ayyar <karthik@houseofkodai.in>

20130810

1. Introduction
---------------
   An Open Data Element (ODE) is a self-defining structure of data that
   is easy to generate and parse. ODE is a basic building block
   file-formats and network-protocols. Network-protocols are essentially
   exchange of ODE between peers; This ODE eXchange is known as OX.

   Although each element is restricted in size of upto 65533
   (2^16:65536-3) bytes, larger data can be encoded as a sequence of
   ODEs.

   ODE can be used recursively; an ODE can contain other ODEs.

   Defining file-formats and network-protocols is as simple as
   defining the type-table ODE. It is anticipated that application
   developers will publish ODE type-tables encouraging interoperability.

   A network of nodes communicating ODEs via. OX is known as Open Data
   Interface Network (ODIN).

2. Definition
-------------
   [Size][Type][Data]
    [2 bytes Size]
    [1 byte Type]
    [(Size-1) bytes Data]

   [Size]
    for UDP packets, refers to sequence number, as size is implicit
    intended for jitter-buffers to deal with out-of-order/delayed
    packets.

   [Type]
    1 bit of fragment-flag (left-most-bit fragment-flag)
    7 bits of type (decimal values 0-127)

3. ODE0: Reserved Type Table Definition
---------------------------------------
     Type Description
     ----------------
     58 : Type-table definition
          [0+ bytes space delimiter]
          [numeric-string ASCII value of Type]
          [1 byte space delimiter]
          [description]
          [2 byte \n\n new-line new-line description terminator]

     33 ! Identity Element
          1-byte space delimited words
          [id] [status] [params...]
          ! karthik idle version x microphone camera tv speaker

     42 * Group of elements
          [numeric-string number of elements]
          [1 byte ASCII non-numeric delimiter]
          [ODElements]
          multiplex multiple elements into a single element/packet

     63 ? Request
          [command] [params...]

     43 + Response-success
          [response data]

     45 - Response-failure
          [error code numeric]
          [1 byte space delimiter]
          [error code description]

     61 = Response-redirect
          [redirect URL]

      0   Bytes
          [name]
          [1 byte \0 null terminator]
          [data bytes]
          useful for encapsulating files.

     46 . Pointer/Index to previous element
          [2-byte previous-element-offset]
          [(size-2) bytes unique-marker]
          random-file-seek: search for unique-marker and then
          traverse elements as required.
