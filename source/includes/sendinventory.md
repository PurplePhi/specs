## Send Inventory

> Example inventory message

```xml
<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:wsa="http://www.w3.org/2005/08/addressing">
  <soap:Header>
    <wsse:Security xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" soap:mustUnderstand="1">
      <wsse:UsertextToken>
        <wsse:Username>usertext</wsse:Username>
        <wsse:Password Type="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-usertext-token-profile-1.0#PasswordText">password</wsse:Password>
      </wsse:UsertextToken>
    </wsse:Security>
    <wsa:MessageID>8ee65699-8b8b-8427-2822-687da8b55a89</wsa:MessageID>
    <wsa:CorrelationID>9e0205ce-e16b-4131-a44e-d2765cf76a76</wsa:CorrelationID>
    <wsa:To>https://api.pricematch.travel/htng_message</wsa:To>
  </soap:Header>
  <soap:Body>
    <OTA_HotelInvCountNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05">
      <Inventories HotelCode="HXCAIZZ">
        <Inventory>
          <StatusApplicationControl Start="2015-09-01" InvTypeCode="King"/>
          <InvCounts>
            <InvCount CountType="1" Count="20"/>
            <InvCount CountType="2" Count="15"/>
            <InvCount CountType="4" Count="3"/>
            <InvCount CountType="5" Count="2"/>
            <InvCount CountType="3" Count="0"/>
            <InvCount CountType="6" Count="0"/>
            <InvCount CountType="8" Count="0"/>
          </InvCounts>
        </Inventory>
        <Inventory>
          <StatusApplicationControl Start="2015-09-01" InvTypeCode="DBL"/>
          <InvCounts>
            <InvCount CountType="1" Count="13"/>
            <InvCount CountType="2" Count="10"/>
            <InvCount CountType="4" Count="1"/>
            <InvCount CountType="5" Count="2"/>
            <InvCount CountType="3" Count="0"/>
            <InvCount CountType="6" Count="0"/>
            <InvCount CountType="8" Count="0"/>
          </InvCounts>
        </Inventory>
        <Inventory>
          <StatusApplicationControl Start="2015-09-02" InvTypeCode="King"/>
          <InvCounts>
            <InvCount CountType="1" Count="20"/>
            <InvCount CountType="2" Count="10"/>
            <InvCount CountType="4" Count="9"/>
            <InvCount CountType="5" Count="1"/>
            <InvCount CountType="3" Count="0"/>
            <InvCount CountType="6" Count="0"/>
            <InvCount CountType="8" Count="0"/>
          </InvCounts>
        </Inventory>
        <Inventory>
          <StatusApplicationControl Start="2015-09-02" InvTypeCode="DBL"/>
          <InvCounts>
            <InvCount CountType="1" Count="13"/>
            <InvCount CountType="2" Count="5"/>
            <InvCount CountType="4" Count="6"/>
            <InvCount CountType="5" Count="2"/>
            <InvCount CountType="3" Count="0"/>
            <InvCount CountType="6" Count="0"/>
            <InvCount CountType="8" Count="0"/>
          </InvCounts>
        </Inventory>
      </Inventories>
    </OTA_HotelInvCountNotifRQ>
  </soap:Body>
</soap:Envelope>
```

The Hotel Inventory Count message provides the ability for a PMS to post inventory amounts.

We support the following inventory codes (CountType in the message body):

* `1` **Physical** - number of rooms/units physically existing on the property
* `2` **Definitive availability** - number of rooms/units available, minus number of allotments still available
* `3` **Tentative availability** - number of allotments still available
* `4` **Definitive sold** - number of rooms/units sold - status is confirmed
* `5` **Tentative sold** - number of rooms/units sold - status is option
* `6` **Out of order** - number of rooms/units temporarily unavailable (e.g. remodel, repair, etc.)
* `8` **Out of inventory** - number of rooms/units which are indefinitely unavailable (e.g. construction)

<aside class="warning">You must always use all the CountTypes 1, 2, 3, 4, 5, 6 and 8 in every message (even if Count = 0).</aside>

<aside class="notice">The resulting occupancy will be computed this way:<br>
<b>(Definitive sold + Tentative sold + Out of order) / (Physical - Out of inventory)</b></aside>

You should send an update for all the dates and room types modified after each inventory modification.

It is also required to send a full inventory update every 24 hours to correct any missed messages.

If it is not possible to send only the dates and room types modified, instead send a full update every hour (all room types, full horizon 365 days).

### Fields we consider

|  |  |
| --- | --- |
| `<Inventories HotelCode="HXCAIZZ">` | The `Hotel Code` variable maps the message to a specific property. This code must be defined by you. |
| `<Inventory>` | One `<Inventory>` block is given for one date and one room type of the inventory update.<aside class="notice">If 2 room types are updated, there will be at least 2 Inventory blocks. If 2 dates are updated, there will be at least 2 Inventory blocks.</aside> |
| `<StatusApplicationControl Start="2015-09-01">`   | `Start` is the stay date to be processed. |
| `<StatusApplicationControl InvTypeCode="King">` | `InvTypeCode` is the room type code. |
| `<InvCount CountType="1" Count="20"/>`| One `<InvCount>` block defines the inventory count for one given status, for the considered date and room type.<br><br>The `CountType` defines the room status based on the list above.<br>The `Count` is the value of the updated inventory. |