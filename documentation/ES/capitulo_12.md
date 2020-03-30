<br>
<h1 align="center">Capitulo 12</h1>
<br>

# 12. Tabla de operaciones para los mensajes de ruta multicast

## 12.1 fetch_rte_msg_table_entry

```cpp
/* Find an entry in the RteMsg table matching the given 
 message's msg-type, OrigAddr, TargAddr, MetricType */

fetch_rte_msg_table_entry (rteMsg)
{
 foreach (entry in RteMsgTable)
 {
 if (entry.msg-type == rteMsg.msg-type 
 AND entry.OrigAddr == rteMsg.OrigAddr 
 AND entry.TargAddr == rteMsg.TargAddr 
 AND entry.MetricType == rteMsg.MetricType)
 return entry;
 }
 return NULL;
}
```

## 12.2 update_rte_msg_table
```cpp
/* Update the multicast route message suppression table based on the 
 received RteMsg, return true if it was created or the SeqNum was 
 updated (i.e. it needs to be regenerated) */

update_rte_msg_table(rteMsg)
{
 /* search for a comparable entry */
 entry = Fetch_Rte_Msg_Table_Entry(rteMsg);

 /* if there is none, create one */
 if (entry does not exist)
 {
 entry.MessageType = rteMsg.msg_type;
 entry.OrigAddr = rteMsg.OrigAddr;
 entry.TargAddr = rteMsg.TargAddr;
 entry.OrigSeqNum = rteMsg.origSeqNum; // (if present)
 entry.TargSeqNum = rteMsg.targSeqNum; // (if present)
 entry.MetricType = rteMsg.MetricType; 
 entry.Metric = rteMsg.OrigMetric; // (for RREQ)
 or rteMsg.TargMetric; // (for RREP) 
 entry.Timestamp = CurrentTime;
 return TRUE;
 }

 /* if current entry is stale */
 if (
 (rteMsg.msg-type == RREQ AND entry.OrigSeqNum < rteMsg.OrigSeqNum)
 OR
 (rteMsg.msg-type == RREP AND entry.TargSeqNum < rteMsg.TargSeqNum))
 {
 entry.OrigSeqNum = rteMsg.OrigSeqNum; // (if present)
 entry.TargSeqNum = rteMsg.TargSeqNum; // (if present)
 entry.Timestamp = CurrentTime;
 return TRUE;
 }

 /* if received rteMsg is stale */
 if ( 
 (rteMsg.msg-type == RREQ AND entry.OrigSeqNum > rteMsg.OrigSeqNum)
 OR
 (rteMsg.msg-type == RREP AND entry.TargSeqNum > rteMsg.TargSeqNum))
 {
 entry.Timestamp = CurrentTime;
 return FALSE;
 }

 /* if same SeqNum but rteMsg has lower metric */
 if (entry.Metric > rteMsg.Metric)
 entry.Metric = rteMsg.Metric;

 entry.Timestamp = CurrentTime;
 return FALSE;
}
```