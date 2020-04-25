
# 9. Packet format for MANET networks.

_AODVv2_ specifies that control messages have to be mapped to a container called MANET (Generalized Mobile Ad-Hoc Network Packet/Message Format) [RFC5444]. This packet format provides a single encapsulation for multiple Ad-Hoc routing protocols.

RFC5444 makes the transmission of control messages more efficient, structuring the content by reducing the number of bytes to transmit.

The RFC5444 format defines the following elements:
- Packet: It is the highest level entity. A packet contains a header and zero or more messages.
- Message: It is the entity that transports the protocol information. A message consists of a header, a TLV (type-length-value) block, and an addresses block.
- Addresses block: consists of one or more addresses, and an attribute block.
- TLV Block: It consists in one or more TLVs.
- TLV: It is a structure (type-length-value) where: 
 - Type: It is the identifier of the data type that follows.
 - Length: This field indicates how many bytes the field value occupies.
 - Value: It is the concrete value of the object to which it refers.

The following image represents the structure of an _RFC544_ package and its dependencies.

 <img src="imple_pic/rfc5444-pkt.png" alt="drawing" height="600" width="1000" align="center"/>

Each type of control message has to be adapted to the format of the _RFC5444_ packet.

_AODVv2_ doesn´t require access to the packet header [RFC5444].

The _AODVv2_ packet header use:
- **msg-type**. 
- **msg-hop-limit**.
- **msg-addr-length**.

**msg-addr-length** indicates the size of the address in the message, whose value corresponds to _addr_length in octets -1_; for example, for _IPV4_ it would be 3 and for _IPV6_ it would be equal to 15.

This is why we will first review which are the header fields of the _RFC5444_ packet that _AODVv2_ uses.



## 9.1 Packet info.
This section of the message allows to quickly know information such as:

- Packet version.
- Sequence number.
- _Flags_ that allow knowing if the TLVs are in the packet without deserializing it.

### 9.1.1 pkt-header

We define it as follows:

**version**: It is a field that contains a 4-bit unsigned integer and specifies the version in which the packet and the message content have been built.

**pkt-flag**: It is a 4-bit field that specifies the interpretation of the rest of the packet header:

 - **bit 0** (phaseseqnum) if one ('1') is indicated, then it means that the **pkt-seq-num** is included in the **pkt-header**. Otherwise, it is not.

 - **bit 1** (phastlv): If one ('1') is indicated, it means that the **tlv-block** is included in the **pkt-header**. Otherwise, it is not.

 - **bit 2-3**: They are reserved and should be cleaned in each transmission and must be ignored in each reception.
 
**pkt-seq-num**: It is ignored if the _phaseseqnum-flag_ is set to zero ('0'). Otherwise, it is a 16-bit unsigned integer, specifying the sequence number of a packet.

**tlv*block**: It is ignored if the _phastlv-flag_ is set to zero ('0'). Otherwise, it is defined in [RFC4544 seccion 5.4](https://tools.ietf.org/html/rfc5444#section-5.2)



## 9.2 Messages.
Packets can contain the packet header, and one or more messages. The messages contain:

 - A message header.
 - A TLV message block that contains zero or more TLVs, associated with the complete message.
 - Zero or more address blocks, each block contains one or more addresses objects.
 - An address block TLV block, which contains zero or more TLVs.

```
 <message> = <msg-header>
 <tlv-block>
 (<addr-block><tlv-block>)*

 <msg-header> = <msg-type>
 <msg-flags>
 <msg-addr-length>
 <msg-size>
 <msg-orig-addr>?
 <msg-hop-limit>?
 <msg-hop-count>?
 <msg-seq-num>?
```

### 9.2.1 msg-header

**msg-type**: It is a field that houses an 8-bit unsigned integer, specifying the type of message.

**msg-flags**: It is a 4-bit field that specifies the interpretation of the rest of the message header:

 - **bit 0**(mhasorig): If set to one ('1') it means that **msg-orig-addr** will be included in **msg-header**. Otherwise, no.
 - **bit 1**(mhashoplimit): If set to one ('1'), it means that **msg-hop-limit** will be included in **msg-header**. Otherwise, no.
 - **bit 2**(mhashopcount): If set to one ('1'), it means that **msg-hop-count** will be included in **msg-header**. Otherwise, no.
 - **bit 3**(mhaseqnum): If it is set to one ('1'), then it means that **msg-seq-num** will be included in **msg-header**. Otherwise, no.

### 9.2.2 msg-header-length
**ONGOING PROCESS**

It is a field that contains an 8-bit unsigned integer

_AODVv2_ uses the following fields from the _RFC5444_ Header message

<img src="imple_pic/header-rfc5444.png" alt="drawing" height="100" width="400" align="center"/>

<br>

<h3> The address block is made up of::</h3>

<img src="imple_pic/tlv-addr-block.png" alt="drawing" height="100" width="460" align="center"/>

<h3> TLV for _OrigPrefix_ will be formed by:</h3>

<img src="imple_pic/tlvOrigPrefix.png" alt="drawing" height="120" width="460" align="center"/>

<h3>The TLV for _OrigPrefix_ will be formed of:</h3>

<img src="imple_pic/tlvTargetPrefix.png" alt="drawing" height="120" width="460" align="center"/>


## 9.3 oonf_api
For the encapsulation of the _AODVv2_ package within the _RFC5444_ package container, the API called [oon_api](https://github.com/benpicco/oonf_api), is used, which facilitates the reading and writing of said package within the container.

