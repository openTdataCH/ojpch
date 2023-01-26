# Mapping for OJPFareRequest to TOMP "planning/inquiries" and "planning/offers" request

The OJP fare service does not really provide booking, but assigns prices to trips (TRIP) or trip legs (LEG). However, this creates a hiatus between OJP and TOMP:

*   OJP assumes price indication/or concrete prices but does not assume that a booking will follow immediately afterwards. It might also work with variants.
*   TOMP integrates the price information as a first step of the booking process. Usually the internal planning system of the service provider already does a reservation for the leg.

For the usage in OJP fare best practice for the implementation of TOMP or the conversion step, is to use a system that allows high volume, variants and does not already create booking sessions and keep the possible reservations alive for a givne time. Therefore the normal implementation is with "planning/inquries", but needs the price which is optional. Otherwise people wanting to having the prices might be forced to use the heavy duty "planning/offers" interface.  The OJP fare service assumes a non-blocking, non-reservating service from TOMP. An offer id, however, will be passed to the client system with OJP 2.0 

 In Switzerland an implementation of "planning/inquries" which also returns and indicative price is strongly suggested for mobility providers.

  

Following table describes the values which can be used in an [OJPFares Request](https://github.com/VDVde/OJP/blob/master/OJP_Fare.xsd) to use as elements in [TOMP "planning/inquiry"](https://app.swaggerhub.com/apis-docs/TOMP-API-WG/transport-operator_maas_provider_api/1.3.0#/planning/post_planning_inquiries) Request. We are only using TripFareRequest with parameters..

<table class="relative-table confluenceTable"  style="width: 90.9521%;">
   <colgroup>
      <col style="width: 37.8629%;">
      <col style="width: 28.7188%;">
      <col style="width: 13.6012%;">
      <col style="width: 19.8045%;">
   </colgroup>
   <tbody>
      <tr>
         <th class="confluenceTh"><span style="color: rgb(0,0,0);">OJP Fares Request</span></th>
         <th class="confluenceTh"><span style="color: rgb(0,0,0);">TOMP planning/inquiry Request</span></th>
         <th class="confluenceTh"><span style="color: rgb(0,0,0);">Remarks</span></th>
         <th class="confluenceTh"><span style="color: rgb(0,0,0);">Action</span></th>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.ErrorMessage.Code</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">error.errorcode (HTTP)</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">-</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.ErrorMessage.Text.Text</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">error.detail (HTTP)</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">-</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult. ErrorMessage.Text.TextId</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">-</span></td>
      </tr>
      <tr>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.TriPFareRequest. Trip <strong>TripStructure</strong></span></td>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);"><strong>from/to</strong></span></td>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);">Structures which contain trip that is used to build the from/to for each TOMP request. Be aware that this may be need to split into multiple TOMP request. One per leg that needs a price.</span></td>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);">Process through each leg</span></td>
      </tr>
      <tr>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.TriPFareRequest.TripLeg.LegStart/LegEnd.<strong>Choice</strong></span></p>
            <ul>
               <li><span style="color: rgb(0,0,0);">StopPointRef (NMToken): the logical stop element (may be on the level of a STOP PLACE or QUAY)</span></li>
               <li><span style="color: rgb(0,0,0);">StopPlaceRef (normalizedString): The physical stop element</span></li>
               <li><span style="color: rgb(0,0,0);">GeoPosition ( siri:LocationStructure): The coordinates as WGS84</span></li>
            </ul>
            <p><span style="color: rgb(0,0,0);">The following Types should be ignored in OJPFare in Switzerland:</span></p>
            <ul>
               <li><span style="color: rgb(0,0,0);">TopographicPlaceRef</span></li>
               <li><span style="color: rgb(0,0,0);">PointOfInterestRef</span></li>
               <li><span style="color: rgb(0,0,0);">AddressRef&nbsp;</span></li>
            </ul>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <ul>
               <li>
                  <span style="color: rgb(0,0,0);"><em>from/to.stopReference.type </em><em>ENUM:&nbsp;</em></span>
                  <ul>
                     <li><span style="color: rgb(0,0,0);"><em>GTFS_STOP_ID</em></span></li>
                     <li><span style="color: rgb(0,0,0);"><em>GTFS_STOP_CODE</em></span></li>
                     <li><span style="color: rgb(0,0,0);"><em>GTFS_AREA_ID</em></span></li>
                     <li><span style="color: rgb(0,0,0);"><em>CHB_STOP_PLACE_CODE</em></span></li>
                     <li><span style="color: rgb(0,0,0);"><em>CHB_QUAY_CODE</em></span></li>
                     <li><span style="color: rgb(0,0,0);"><em>NS_CODE</em></span></li>
                  </ul>
               </li>
               <li>
                  <span style="color: rgb(0,0,0);"><em>from/<a href="http://to.place.stopReference.id" class="external-link" rel="nofollow">to.place.stopReference.id</a>: examples</em><em>:</em></span>
                  <ul>
                     <li><span style="color: rgb(0,0,0);"><em>NL:S:13121110</em></span></li>
                     <li><span style="color: rgb(0,0,0);"><em>BE:S:79640040</em></span></li>
                  </ul>
               </li>
               <li>
                  <span style="color: rgb(0,0,0);"><em>from/to.place.stopReference.country (</em><em>two-letter country codes according to ISO 3166-1). </em><em>Example:</em></span>
                  <ul>
                     <li><span style="color: rgb(0,0,0);"><em>NL</em></span></li>
                     <li><span style="color: rgb(0,0,0);"><em>CH</em></span></li>
                  </ul>
               </li>
            </ul>
            <p><br></p>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <ol>
               <li><span style="color: rgb(0,0,0);">StopReference Object in TOMP is optional. If the data is used in OJPFare, it should be filled.&nbsp; The Swiss TOMP Profile intends to always provide the coordinates.</span></li>
               <li><span style="color: rgb(0,0,0);">Documentation on TOMP:&nbsp; "reference to a stop (can be nation specific). This can help to specific pinpoint a (bus) stop. Extra information about the stop is not supplied; you should find it elsewhere."</span></li>
               <li><span style="color: rgb(0,0,0);">The ENUM's of stopReferenceType are not explained. We had to make some assumptions.</span></li>
            </ol>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>Mapping:</em></span></p>
            <ul>
               <li><span style="color: rgb(0,0,0);"><em>StopPointRef → GTFS_STOP_ID</em></span></li>
               <li><span style="color: rgb(0,0,0);"><em>StopPlaceRef → CHB_STOP_PLACE_CODE</em></span></li>
               <li><span style="color: rgb(0,0,0);"><em>Currently the DIDOK is used for the <a href="http://stopReference.id" class="external-link" rel="nofollow">stopReference.id</a></em></span></li>
            </ul>
            <p><span style="color: rgb(0,0,0);"><img class="emoticon emoticon-warning" src="images/icons/emoticons/warning.svg" data-emoticon-name="warning" alt="(Warnung)"> TOMP should add a documentation to the ENUM</span></p>
            <p><br></p>
         </td>
      </tr>
      <tr>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.TripFareRequest. TripLeg.Service.OperatorRefs</span></td>
         <td style="text-align: left;" class="confluenceTd">n/a</td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">&nbsp;However, the OperatorRefs tell which endpoint to use. Therefore the OperatorRefs must be processed to get the necessary end points with the Service Catalogue.</span></p>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <div class="content-wrapper">
               <p><span style="color: rgb(0,0,0);">must be used to get the end point. If an end points provides several operators it might be needed to add this as well.</span></p>
               <p><span style="color: rgb(0,0,0);">Nothing found in TOMP</span></p>
            </div>
            <p><img class="emoticon emoticon-warning" src="images/icons/emoticons/warning.svg" data-emoticon-name="warning" alt="(Warnung)">TOMP: Operators could be added to operators for systems that serve more than one operator.</p>
         </td>
      </tr>
      <tr>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>from/to.stationId&nbsp;</em></span></p>
            <p><br></p>
            <p><span style="color: rgb(0,0,0);"><em><br></em></span></p>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Documentation on TOMP: "<span>reference to operator/stations". The stations describe the pick up spots of the vehicles (e.g. eScooter stations)</span></span></p>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed&nbsp;</span></p>
            <p><img class="emoticon emoticon-warning" src="images/icons/emoticons/warning.svg" data-emoticon-name="warning" alt="(Warnung)">OJP: How to deal with this must be studied.</p>
         </td>
      </tr>
      <tr>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><strong>Possibilities to get the coordinates&nbsp; *</strong></span></p>
            <ol>
               <li><span style="color: rgb(0,0,0);"><strong>OJP.OJPFareRequest.TripFareRequest.TripLeg.LegStart/LegEnd.CallPlace. GeoPosition.Longitude</strong></span></li>
               <li><span style="color: rgb(0,0,0);"><strong>Client uses OJP.LocationInformationRequestto get GeoLocation</strong></span></li>
               <li><span style="color: rgb(0,0,0);"><strong>Server is able to search for GeoPosition using the References (StopPointRef, StopPlaceRef, TopographicPlaceRef, PointOfInterestRef, AddressRef)</strong></span></li>
            </ol>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">from<em>/to</em>.coordinated.lng</span></p>
            <p><span style="color: rgb(0,0,0);">from<em>/to</em>.place.coordinated.lat</span></p>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">No coordinate information in OJPFares if the CallPlace is not of type GeoPosition. Perhaps from the TripContext or obtained by using OJP Location Information service.</span></p>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <div class="content-wrapper">
               <ol>
                  <li style="list-style-type: none;">
                     <ol>
                        <li><span style="color: rgb(0,0,0);">The client should always send coordinates to a TOMP system.</span></li>
                        <li><span style="color: rgb(0,0,0);">It is therefore crucial, that this information can be added at some point.</span></li>
                        <li><span style="color: rgb(0,0,0);">Using stopReference.type and <a href="http://stopReference.id" class="external-link" rel="nofollow">stopReference.id</a> might be needed nevertheless for optimal response.</span></li>
                     </ol>
                  </li>
               </ol>
               <p><span style="color: rgb(0,0,0);"><img class="emoticon emoticon-warning" src="images/icons/emoticons/warning.svg" data-emoticon-name="warning" alt="(Warnung)"> OJP: OJP should consider adding TripContext so that doing LIR in the converter is not needed.</span></p>
            </div>
         </td>
      </tr>
      <tr>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);"><em>from/to.place.coordinates.alt</em></span></td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">No altitude element in OJP.&nbsp;</span></p>
            <p><span style="color: rgb(0,0,0);">Possible use-case: Operator wants to use altitude difference from start to stop point for fare price calculation (example: eScooter uses more energy to get up a hill)</span></p>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Not supported</span></p>
         </td>
      </tr>
      <tr>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">n/a</span></p>
         </td>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);"><em>from/to.place.physicalAddress</em></span></td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Mapping AddressRef in OJP to phyiscalAddress in TOMP is hardly possible, as AddressRef is a normalizedString while physicalAddress is a structure.</span></p>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Not supported</span></p>
         </td>
      </tr>
      <tr>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td style="text-align: left;" class="confluenceTd"><span style="color: rgb(0,0,0);"><em>radius</em></span></td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">documentation for radius on TOMP: "<span>Maximum distance in meters a user wants to travel to reach the travel option."</span></span></p>
            <p><span style="color: rgb(0,0,0);">Is only of interest to define a trip, when asking for a fare, the trip has already been defined. Not needed with OJPFares</span></p>
         </td>
         <td style="text-align: left;" class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Not supported</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.TripFareRequest. Trip[n].Distance</span></p>
            <p><span style="color: rgb(0,0,0);"><strong>OJP.OJPFareRequest.TripFareRequest.Trip. TimedLeg.LegTrack.TrackSection.Length&nbsp;</strong></span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">estimatedDistance</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Documentation for estimatedDistance on TOMP: "<span>instead of using the from/to construct, it is also possible to give an indication of the distance to travel. The process identifier 'USE_ESTIMATED_DISTANCE' is used to indicate this scenario. Also in meters"</span></span></p>
            <p><span style="color: rgb(0,0,0);">In OJP this means that per leg the a LegTrack.TracSection must exist with the element LENGTH</span></p>
         </td>
         <td class="confluenceTd">
            <div class="content-wrapper">
               <p><span style="color: rgb(0,0,0);">If an operator wants to create a fare price for a distance without having the need to do the routing himself, OJP should provide such information.</span></p>
            </div>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p style="text-align: left;"><span style="color: rgb(0,0,0);"><strong>OJP.OJPFareRequest.TripFareRequest. Trip.TripLeg.TimedLeg/ContinuousLeg.Leg. ServiceDeparture.TimetabledTime</strong></span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);"><em>departureTime</em></span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><br></td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p style="text-align: left;"><span style="color: rgb(0,0,0);"><strong>OJP.OJPFareRequest.TripFareRequest.Trip.TripLeg.TimedLeg/ContinuousLeg.Leg.ServiceArrival.TimetabledTime</strong></span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);"><em>arrivalTime</em></span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><br></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.Params.Traveller</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);"><em>nrOfTravellers</em></span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">No element for number of travellers in OJP. Would need to be calculated from the number of Traveller elements (or set to 1, if not available)</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Travellers is always set to 1</span></p>
            <p><br></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.Params.Traveller.Age</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);"><em>travelers.traveller.age</em></span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Set to 20, if no Traveller exists.</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);"><em>travelers.traveller.isValidated</em></span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">information if the travelers identity and properties have been verified by the MaaS provider</span></td>
         <td class="confluenceTd">
            <div class="content-wrapper">
               <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
            </div>
            <p><span style="color: rgb(0,0,0);"><img class="emoticon emoticon-warning" src="images/icons/emoticons/warning.svg" data-emoticon-name="warning" alt="(Warnung)"> OJP: Check if OJP might need this for OJP 2.0</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);"><em>travelers.traveller.referenceNumber</em></span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">No referenceNumber of traveller found in OJP Fares. Would only work with a global referenceNumber? Where one passenger would have the same number for every operator?</span></td>
         <td class="confluenceTd">
            <div class="content-wrapper">
               <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
               <p><span style="color: rgb(0,0,0);"><img class="emoticon emoticon-warning" src="images/icons/emoticons/warning.svg" data-emoticon-name="warning" alt="(Warnung)"> OJP: Check if OJP might need this for OJP 2.0</span></p>
            </div>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.Params.<strong>EntitlementProduct:&nbsp;</strong>Doc: [a specific form of TRAVEL DOCUMENT in TM and NeTEx] a precondition to access a service or to purchase a FARE PRODUCT issued by an organisation that may not be a PT operator (eg: military card, concessionary card, etc)&nbsp;Is of type NMTOKEN.</span></p>
            <p><span style="color: rgb(0,0,0);">AND</span></p>
            <p><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.Params.Traveller.<strong>PassengerCategory: </strong>Doc: sequence of all passenger categories, for which this FareProduct is valid. Enum Adult, Child, Senior, Youth, Disabled.</span></p>
            <p><span style="color: rgb(0,0,0);">AND</span></p>
            <p><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.Params.<strong>SalesPackageElementRef: </strong>Doc: Id of a FareProduct that the passenger already holds and that may be used for the travel or parts of it. Is of type NMTOKEN.</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>travelers.traveler.cardTypes</em> cardTypeStructure: TOMP documentation on cardTypeStructure: "A generic description of a card, asset class and acceptors is only allowed for DISCOUNT/TRAVEL/OTHER cards. Card types: [ ID, DISCOUNT, TRAVEL, BANK, CREDIT, PASSPORT, OTHER ]</span></p>
            <p><span style="color: rgb(0,0,0);">type&nbsp; Enum:</span><br><span style="color: rgb(0,0,0);">[ ID, DISCOUNT, TRAVEL, BANK, CREDIT, PASSPORT, OTHER ]</span></p>
            <p><span style="color: rgb(0,0,0);">subType &nbsp; &nbsp;string</span><br><span style="color: rgb(0,0,0);">For use in case of OTHER. Can be used in bilateral agreements.</span></p>
            <p><span style="color: rgb(0,0,0);">assetClass&nbsp; &nbsp;</span><br><span style="color: rgb(0,0,0);">These classes are taken from the NeTeX standard, but ALL and UNKNOWN are removed. On the other hand OTHER and PARKING are added.</span></p>
            <p><span style="color: rgb(0,0,0);">Enum:</span><br><span style="color: rgb(0,0,0);">[ AIR, BUS, TROLLEYBUS, TRAM, COACH, RAIL, INTERCITYRAIL, URBANRAIL, METRO, WATER, CABLEWAY, FUNICULAR, TAXI, SELFDRIVE, FOOT, BICYCLE, MOTORCYCLE, CAR, SHUTTLE, OTHER, PARKING, MOPED, STEP ]</span></p>
            <p><span style="color: rgb(0,0,0);">acceptors &nbsp; &nbsp;[</span><br><span style="color: rgb(0,0,0);">references to accepting parties, only if applicable"</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">cardTypes could be used by the operator to define fare products. From the OJP Elements EntitlementProduct and PassengerCategory fits this narrative. Although it is only defined as NMTOKEN or ENUM values and does not provide the granularity of TOMP.</span></p>
            <p><span style="color: rgb(0,0,0);">Also EntitlementProduct is atm not defined by traveller, but for the whole request.</span></p>
            <p><br></p>
            <p><br></p>
            <p><br></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>travelers.traveler.licenseType: </em><em>licenseTypeStructure</em></span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Describes the licences a traveller has to use specific vehicle types (car, etc.). NO OJP Fares element to forward this data.</span></p>
            <p><span style="color: rgb(0,0,0);">Not needed for fare information, but must be added for the availability requests</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>travelers.traveler.requirements</em></span></p>
            <p><span style="color: rgb(0,0,0);"><em>requirementsStructure</em></span></p>
            <p><span style="color: rgb(0,0,0);"><em>Requirements from the end user side.</em></span></p>
            <p><span style="color: rgb(0,0,0);">source &nbsp; &nbsp;string</span><br><span style="color: rgb(0,0,0);">if obsolete, it is referencing the travelers' dictionary (<a href="https://github.com/TOMP-WG/TOMP-API/blob/master/documents/CROW%20passenger%20characteristics.xlsx" class="external-link" rel="nofollow">https://github.com/TOMP-WG/TOMP-API/blob/master/documents/CROW%20passenger%20characteristics.xlsx</a>)</span></p>
            <p><span style="color: rgb(0,0,0);">category* &nbsp; &nbsp;string</span><br><span style="color: rgb(0,0,0);">references to the first column of the specification initial values [ HR, AV, HV, AB, AER, K, ZR, RR ]</span></p>
            <p><span style="color: rgb(0,0,0);">number* &nbsp; &nbsp;string</span><br><span style="color: rgb(0,0,0);">minLength: 2</span><br><span style="color: rgb(0,0,0);">maxLength: 2</span><br><span style="color: rgb(0,0,0);">references to the second column of the specification</span></p>
            <p><span style="color: rgb(0,0,0);">type &nbsp; &nbsp;string</span><br><span style="color: rgb(0,0,0);">conditionally extra information, referencing to the 3th column</span></p>
            <p><span style="color: rgb(0,0,0);">memo &nbsp; &nbsp;string</span><br><span style="color: rgb(0,0,0);">extra field for detailed information, not standardized</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Not needed to get Fare information.</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>travelers.traveler.knownIdentifier: </em>identifier for this traveler in the personal data store. This identifier can be used to get personal information from the provider specified in the "knownIdentifierProvider"</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Not needed for Fare information</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>travelers.traveler.knownIdentifierProvider: </em>provider for personal information. Can be a URI or identifier.</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Not needed for Fare information</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.OJPFareRequest.Params.ZonesAlradyPaid</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);"><em>n/a</em></span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">No concept found in "planning/inquiry" to take into account when a zone has already been paid.</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>useAssets: </em>The specific asset(s) the user wishes to receive leg options for</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Not needed for Fare information</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>userGroups: </em>Id(s) of user groups that the user belongs to. This provides access to exclusive assets that are hidden to the public. Id's are agreed upon by TO and MP.</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Not needed for Fare information</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">atm not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><em>useAssetTypes: </em>The specific asset type(s) the user wishes to receive leg options for</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Not needed for Fare information</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
   </tbody>
</table>

Example OJPFareRequest
----------------------

```java
<?xml version="1.0" encoding="UTF-8"?>
<OJP xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://www.siri.org.uk/siri" xmlns:ojp="http://www.vdv.de/ojp" version="1.1-dev" xsi:schemaLocation="http://www.siri.org.uk/siri ../../../OJP.xsd">
	<OJPRequest>
		<ServiceRequest>
			<RequestTimestamp>2020-01-19T12:00:00Z</RequestTimestamp>
			<RequestorRef>MyClient</RequestorRef>
			<ojp:OJPFareRequest>
				<RequestTimestamp>2020-01-19T12:00:00Z</RequestTimestamp>
				<MessageIdentifier>231231-231</MessageIdentifier>
				<ojp:TripFareRequest>
					<ojp:Trip>
						<ojp:Id>192391231</ojp:Id>
						<ojp:Duration>PT55M</ojp:Duration>
						<ojp:StartTime>2020-01-19T12:00:02Z</ojp:StartTime>
						<ojp:EndTime>2020-01-19T12:57:00Z</ojp:EndTime>
						<ojp:Transfers>0</ojp:Transfers>
						<ojp:Leg>
							<ojp:Id>1231123</ojp:Id>
							<ojp:ContinuousLeg>
								<ojp:LegStart>
									<ojp:GeoPosition>
										<Longitude>5.1</Longitude>
										<Latitude>20.1</Latitude>
									</ojp:GeoPosition>
									<ojp:Name>
										<ojp:Text>Origin</ojp:Text>
									</ojp:Name>
								</ojp:LegStart>
								<ojp:LegEnd>
									<ojp:GeoPosition>
										<Longitude>5.2</Longitude>
										<Latitude>20.2</Latitude>
									</ojp:GeoPosition>
									<ojp:Name>
										<ojp:Text>Destination</ojp:Text>
									</ojp:Name>
								</ojp:LegEnd>
								<ojp:Service>
									<ojp:ContinuousMode>demandResponsive</ojp:ContinuousMode>
									<ojp:OperatingDayRef>2023-12-24</ojp:OperatingDayRef>
									<VehicleRef>ch:1:vehicle:31231:12311</VehicleRef>
									<ojp:JourneyRef>ch:1:jyid:12931231</ojp:JourneyRef> <!-- mandatory and I don't like this -->
									<LineRef>ch:1:slnid:1231912</LineRef>
									<DirectionRef>ch:1:direction:H</DirectionRef>
									<ojp:Mode>
										<ojp:PtMode>bus</ojp:PtMode>
										<BusSubmode>demandAndResponseBus</BusSubmode>
									</ojp:Mode>
									<ojp:PublishedServiceName>
										<ojp:Text>Mybuxi</ojp:Text>
									</ojp:PublishedServiceName>
									<ojp:BookingArrangements>
										<ojp:BookingArrangement>
											<ojp:BookingUrl>
												<ojp:Label>
													<ojp:Text>MyBuxi</ojp:Text>
												</ojp:Label>
												<ojp:Url>https://mybuxi.ch/booking</ojp:Url>
											</ojp:BookingUrl>
										</ojp:BookingArrangement>
									</ojp:BookingArrangements>
								</ojp:Service>
								<ojp:Duration>PT15M</ojp:Duration>
							</ojp:ContinuousLeg>
							<ojp:EmissionCO2>
								<ojp:KilogramPerPersonKm>0.1</ojp:KilogramPerPersonKm>
							</ojp:EmissionCO2>
						</ojp:Leg>
						<ojp:Leg>
							<ojp:Id>8182381231</ojp:Id>
							<ojp:TransferLeg>
								<ojp:TransferMode>walk</ojp:TransferMode>
								<ojp:LegStart>
									<ojp:GeoPosition>
										<Longitude>5.2</Longitude>
										<Latitude>20.2</Latitude>
									</ojp:GeoPosition>
									<ojp:Name>
										<ojp:Text>Destination</ojp:Text>
									</ojp:Name>
								</ojp:LegStart>
								<ojp:LegEnd>
									<StopPointRef>ch:1:sloid:3000:7</StopPointRef>
									<ojp:Name>
										<ojp:Text>Bern, Gleis 7</ojp:Text>
									</ojp:Name>
								</ojp:LegEnd>
								<ojp:Duration>PT2M</ojp:Duration>
							</ojp:TransferLeg>
						</ojp:Leg>
						<ojp:Leg>
							<ojp:Id>1231123</ojp:Id>
								<ojp:TimedLeg>
								<ojp:LegBoard>
									<StopPointRef>ch:1:sloid:3000:7</StopPointRef>
									<ojp:StopPointName>
										<ojp:Text>Bern, Gleis 7</ojp:Text>
									</ojp:StopPointName>
									<ojp:ServiceDeparture>
										<ojp:TimetabledTime>2020-01-19T13:02:00Z</ojp:TimetabledTime>
									</ojp:ServiceDeparture>
								</ojp:LegBoard>
								<ojp:LegAlight>
									<StopPointRef>ch:1:sloid:7000:33</StopPointRef>
									<ojp:StopPointName>
										<ojp:Text>Zürich HB, Gleis 33</ojp:Text>
									</ojp:StopPointName>
									<ojp:ServiceArrival>
										<ojp:TimetabledTime>2020-01-19T13:57:00Z</ojp:TimetabledTime>
									</ojp:ServiceArrival>
								</ojp:LegAlight>
								<ojp:Service>
									<ojp:OperatingDayRef>2023-01-24</ojp:OperatingDayRef>
									<ojp:JourneyRef>ch:1:sjyid:11:182391231</ojp:JourneyRef>
									<LineRef>ch:1:slnid:11:8</LineRef>
									<ojp:Mode>
										<ojp:PtMode>rail</ojp:PtMode>
										<RailSubmode>highSpeedRailService</RailSubmode>
									</ojp:Mode>
									<ojp:PublishedServiceName>
										<ojp:Text>IC8</ojp:Text>
									</ojp:PublishedServiceName>
									<ojp:DestinationText>
										<ojp:Text>Romanshorn</ojp:Text>
									</ojp:DestinationText>
								</ojp:Service>
							</ojp:TimedLeg>
						</ojp:Leg>
					</ojp:Trip>
				</ojp:TripFareRequest>
				<ojp:Params>
					<ojp:FareAuthorityFilter>ch:1:NOVA</ojp:FareAuthorityFilter>
					<ojp:PassengerCategory>Adult</ojp:PassengerCategory>
					<ojp:TravelClass>second</ojp:TravelClass>
					<ojp:Traveller>
						<ojp:Age>25</ojp:Age>
					</ojp:Traveller>
				</ojp:Params>
			</ojp:OJPFareRequest>
		</ServiceRequest>
	</OJPRequest>
</OJP>

```

  

Example resulting TOMPRequest (for the ContinuousLeg)

  

```java
{
  "from": {
    "coordinates": {
      "lng": 5.1,
      "lat": 20.2
    }
  }, 
  "to": {
    "coordinates": {
      "lng": 5.2,
      "lat": 20.2
    }
  },
  "departureTime": "2020-01-19T12:00:02Z",
  "arrivalTime": "2020-01-19T12:57:02Z",
  "nrOfTravelers": 1,
  "travelers": [
    {
      "age": 25,
    }
  ]
}
```

  

Mapping for TOMP "planning/inquiry" response to OJP Fares response
------------------------------------------------------------------

Following table drescribes the mapping from TOMP to OJP response

<table class="relative-table confluenceTable" style="width: 89.1507%;">
   <colgroup>
      <col style="width: 18.6347%;">
      <col style="width: 43.9729%;">
      <col style="width: 16.0517%;">
      <col style="width: 21.3407%;">
   </colgroup>
   <tbody>
      <tr>
         <th class="confluenceTh"><span style="color: rgb(0,0,0);">TOMP "planning/inquiry" Response</span></th>
         <th class="confluenceTh"><span style="color: rgb(0,0,0);">OJP Fare Response</span></th>
         <th class="confluenceTh"><span style="color: rgb(0,0,0);">Remarks</span></th>
         <th class="confluenceTh"><span style="color: rgb(0,0,0);">Action</span></th>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">error.errorcode (http)</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.ErrorMessage.Code</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><br></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">error.detail (http)</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.ErrorMessage.Text.Text</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><br></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.ErrorMessage.Text.TextId</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not found in TOMP</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FromTripLegId</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: Identifies the "valid from" trip leg.</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">If the goal is to give a fare over multiple legs, this element should be added in TOMP</span></td>
         <td class="confluenceTd">
            <div class="content-wrapper">
               <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
               <p><span style="color: rgb(0,0,0);"><img class="emoticon emoticon-warning" src="images/icons/emoticons/warning.svg" data-emoticon-name="warning" alt="(Warnung)"> TOMP: consider extension of TOMP to process multiple legs in one go from one endpoint.</span></p>
            </div>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.ToTripLegId</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: Identifies the "valid to" trip leg.</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">If the goal is to give a fare over multiple legs, this element should be added in TOMP</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.PassedZones</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Element for passedZones does not exist in TOMP</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);"><a href="http://options.booking.id" class="external-link" rel="nofollow">options.booking.id</a></span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductId</span></p>
            <p><span style="color: rgb(0,0,0);">Typ= NMToken</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Even though an ID is available in the TOMP structure, no id is sent in the "planning/inquiry" response usually and it is discouraged in the documentation. If it is provided, it should be put into the&nbsp;&nbsp;</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">booking id must be mapped to OJPFare.</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePartStructure: Documentation: "this describes a part of the fare (or discount). It contains a for instance the startup costs (fixed) or the flex part (e.g. 1.25 EUR per 2.0 MILES). The amount is tax included. In case of discounts, the values are negative. With 'MAX' you can specify e.g. a maximum of 15 euro per day. Percentage is mainly added for discounts. The <code>scale</code> properties create the ability to communicate scales (e.g. the first 4 kilometers you've to pay EUR 0.35 per kilometer, the kilometers 4 until 8 EUR 0.50 and above it EUR 0.80 per kilometer)."</span></p>
         </td>
         <td class="confluenceTd">
            <p><br></p>
         </td>
         <td class="confluenceTd"><br></td>
         <td class="confluenceTd">
            <p><br></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><a href="http://pricing.fare.parts.farePart.name" class="external-link" rel="nofollow">pricing.fare.parts.farePart.name</a></span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductName</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><br></td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.amount</span></p>
            <p><span style="color: rgb(0,0,0);">type: float</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductPriceGroup.Price</span></p>
            <p><span style="color: rgb(0,0,0);">type: decimal</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><br></td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.amountExVat</span></p>
            <p><span style="color: rgb(0,0,0);">type: float</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductPriceGroup.NetPrice</span></p>
            <p><span style="color: rgb(0,0,0);">type: decimal</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><br></td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.currencyCode</span></p>
            <p><span style="color: rgb(0,0,0);">ISO 4217 currency code</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductPriceGroup.Currency</span></p>
            <p><span style="color: rgb(0,0,0);">ISO 4217 currency code</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OK</span></td>
         <td class="confluenceTd"><br></td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.vatRate</span></p>
            <p><span style="color: rgb(0,0,0);">type: float</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductPriceGroup.VatRate</span></p>
            <p><span style="color: rgb(0,0,0);">enum: no, full, half, mixed, unknown</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">Unclear why VatRate in OJP is an enumeration.</span></p>
            <p><span style="color: rgb(0,0,0);">OJP Enum will be changed in OJP 2.0 → will be percentage</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">to be ignored at the moment</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">options.pricing.fare.class</span></p>
            <p><span style="color: rgb(0,0,0);">type: string</span></p>
            <p><span style="color: rgb(0,0,0);">doc: "in the future we'll set up an enumeration of possible "fare classes". For now it's free format."</span></p>
         </td>
         <td class="confluenceTd">
            <div class="content-wrapper">
               <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductValidityGroup.TravelClass</span></p>
               <p><span style="color: rgb(0,0,0);">Is of type NMToken with enumeration</span></p>
               <span style="color: rgb(0,0,0);">[all, first, second, third, business, economy]</span>
            </div>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Mapping might be difficult from string in TOMP to an enumeration in OJP.&nbsp;</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">If no classes specified, use "all", else for public transport use "first" and "second"</span></p>
            <p><br></p>
            <p><span style="color: rgb(0,0,0);"><img class="emoticon emoticon-warning" src="images/icons/emoticons/warning.svg" data-emoticon-name="warning" alt="(Warnung)">&nbsp; TOMP: Until the "future" has arrived, define the OJP enumeration as best practice in TOMP</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">options.booking.customer.cardTypeStructure</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductValidityGroup.RequiredCard</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: "One or more traveller cards that are needed for purchase of this FareProduct. In most cases traveller cards offer discounts, f.e. BahnCard50 of Deutsche Bahn."</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">In TOMP the card type is described for a specific customer.&nbsp;</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">atm not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <div class="content-wrapper">
               <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductValidityGroup.ValidFor</span></p>
               <p><span style="color: rgb(0,0,0);">is of type string with enumeration</span></p>
               <p><span style="color: rgb(0,0,0);">[Adult, Child, Senior, Youth, Disabled]</span></p>
            </div>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">No validity element found in TOMP, possibly TOMP only gives fare information according to the request parameters in CardTypeStructure&nbsp;</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductValidityGroup.ValidityDuration</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: "Maximum duration of FareProduct validity starting with purchase of ticket or begin of journey (ticket validation)."</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Not needed, as no purchase of ticket is done</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductValidityGroup.ValidiityDurationText</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: "Textual description of maximum validity duration"</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Not needed.</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductValidityGroup.ValidityTariffZones</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: "patial validity of FareProduct defined as list of fare zones."</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Tarif zones concept is not handled in TOMP</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductValidityGroup.ValidityAreaText</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: "Textual description of spatial validity."</span></p>
         </td>
         <td class="confluenceTd"><br></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductBookingGroup.InfoUrl</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: "URL to information for this FareProduct"</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">This information may not be of use for the customer.</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductBookingGroup.SaleUrld</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: "URL to buy the FareProduct online"</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">With planning/inquiries this is probably not used. However this might be filled in by the transformer.</span></p>
            <p><br></p>
            <p><span style="color: rgb(0,0,0);">It is assumed this URL may directly go to the session and not only to the booking service. However, this depends on the implementation. As the provision of an orderid is discouraged, it is not that important.</span></p>
         </td>
         <td class="confluenceTd">
            <div class="content-wrapper">
               <p><span class="placeholder-inline-tasks" style="color: rgb(0,0,0);">Possibly can be filled by information obtained from the&nbsp; Service Catalogue.</span></p>
            </div>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.FareProduct.FareProductBookingGroup.BookingArrangements</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed, for fare information</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
            <p><span class="placeholder-inline-tasks" style="color: rgb(0,0,0);">Possibly can be filled by information obtained from the&nbsp; Service Catalogue.</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">OJP.FareResponse.FareResult.TripFareResult.StaticInfoUrl</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
            <p><span class="placeholder-inline-tasks" style="color: rgb(0,0,0);">Possibly can be filled by information obtained from the&nbsp; Service Catalogue.</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.vatCountryCode</span></p>
            <p><span style="color: rgb(0,0,0);"><span class="property primitive">axLength:&nbsp;2</span><span class="property primitive"><br>minLength:&nbsp;2</span><span class="property primitive"><br>example:&nbsp;NL</span></span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><br></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.type</span></p>
            <p><span style="color: rgb(0,0,0);">[&nbsp;FIXED, FLEX, MAX&nbsp;]</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">This information might be of interest to the customer and should be provided in OJP</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.kind</span></p>
            <p><span style="color: rgb(0,0,0);">[&nbsp;DEFAULT, DISCOUNT, SURGE&nbsp;]</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: is this the default price or is this an additional part (discount, price surge). In case of a DISCOUNT, the amount must always be negative and in case of SURGE it must be positive. This also means, that when you're working with discounts or surges, you have to deliver 2 fareparts, one for the default price and one for the discount/surge. This can be used in combination with as well the fixed price parts as with the flex price parts.</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">Does the handling of price change in fare, due to discount have to be communicated to the customer? Can't it be delivered as an own product?</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.unitType</span></p>
            <p><span style="color: rgb(0,0,0);">[&nbsp;KM, SECOND, MINUTE, HOUR, MILE, PERCENTAGE&nbsp;]</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">unitType is only needed if type FLEX is used</span></p>
            <p><span style="color: rgb(0,0,0);">information of the costs when farePart type FLEX is used, is useful for the customer to get an idea on the price development</span></p>
         </td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.unit</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">TOMP unit → The number of unitTypes (amount of km, seconds, etc.)</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.scaleFrom</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: &nbsp; &nbsp; number($float)</span><br><span style="color: rgb(0,0,0);">minimum: 0</span><br><span style="color: rgb(0,0,0);">in case of scaling, this is the bottom value (f.x. in the first hour 3 CAD, the scaleFrom should contain 0 and the scaleType HOUR). When scaleTo is used, but this field is missing, it should be assumed it is a 0.</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">is scaling information of relevance to the customer?</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.scaleTo</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: minimum: 0</span><br><span style="color: rgb(0,0,0);">the upper value of the scale (f.x. 3 CAD in the first hour, this field should contain 1, scaleFrom 0 and scaleType HOUR)</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">is scaling information of relevance to the customer?</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.scaleType</span></p>
            <p><span style="color: rgb(0,0,0);">Doc: string</span><br><span style="color: rgb(0,0,0);">Enum:</span><br><span style="color: rgb(0,0,0);">[ KM, MILE, HOUR, MINUTE ]</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">is scaling information of relevance to the customer?</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.minimumAmount</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">is information of relevance to the customer to get a fare price for a specific trip?</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.maximumAmount</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">is information of relevance to the customer to get a fare price for a specific trip?</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">atm not needed</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">pricing.fare.parts.farePart.meta</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">empty in doc, not needed</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);">pricing.fare.estimated</span></p>
            <p><span style="color: rgb(0,0,0);">True, False</span></p>
         </td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">No element in OJP FareStructure to define if pricing is an estimation or not</span></td>
         <td class="confluenceTd">
            <p><span style="color: rgb(0,0,0);"><img class="emoticon emoticon-warning" src="images/icons/emoticons/warning.svg" data-emoticon-name="warning" alt="(Warnung)"> OJP: Element should be added to the OJPFareDelivery.</span></p>
         </td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">&nbsp;mainAssetType</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">AssetTypes are handled in accessibility request and not in fare</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
      <tr>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">extraData</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">n/a</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">-</span></td>
         <td class="confluenceTd"><span style="color: rgb(0,0,0);">not needed</span></td>
      </tr>
   </tbody>
</table>

Example of a TOMP planning/inquires response

```java
{
    "validUntil": "2001-12-18T09:30:47Z",
    "options": [
        {
            "id": "1231231112231",
            "legs": [
                {
                    "id": "1231231112231",
                    "from": {
                        "coordinates": {
						       "lng": 5.1,
      							"lat": 20.2 
                        }
                    },
                    "to": {
                        "coordinates": {
                            "lat": 5.2,
                            "lng": 20.2
                        },
                    }, 
                    "assetType": {
                        "id": "bus1",
                        "assetClass": "BUS",
                        "assetSubClass": "bus"
                    },
                    "pricing": {
                        "estimated": false,
                        "parts": [
                            {
 								"name": "Kilometerbasiert normal",
                                "amount": 17,
								"amountExVat": 15,
                                "currencyCode": "CHF"
								"class": "economy"
                            }
                        ]
                    }
                }
            ],
            "state": "NEW"
        }
    ]
}
```

Example of the OJPFareDelivery generated from the TOMP response. In some cases pickup and dropoff may be different from the coordinates added. This needs a refinement request. Also the TOMP service may add foot paths in this case. Either the transformer or the OJP system will have to deal with it. 

The content of BookingArrangement can not be filled from planning/inquiries. Either this information has to come from the Service Catalogue or rom different TOMP requests.

```java
<?xml version="1.0" encoding="UTF-8"?>
<ojp:OJPFareDelivery xmlns:ojp="http://www.vdv.de/ojp" xmlns:siri="http://www.siri.org.uk/siri" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.siri.org.uk/siri  ../../../OJP.xsd">
	<siri:ResponseTimestamp>2001-12-17T09:30:47Z</siri:ResponseTimestamp>
	<siri:Status>true</siri:Status>
	<siri:ValidUntil>2001-12-18T09:30:47Z</siri:ValidUntil>
	<ojp:Problem>
		<ojp:Type>OJPGENERIC_OTHER</ojp:Type>
	</ojp:Problem>
	<ojp:FareResponseContext>
		<ojp:Operators>
			<ojp:Operator>
				<siri:OperatorRef>ch:1:sboid:123123</siri:OperatorRef>
				<siri:OperatorName>Mybuxi</siri:OperatorName>
			</ojp:Operator>
			<ojp:Operator>
				<siri:OperatorRef>ch:1:sboid:11</siri:OperatorRef>
				<siri:OperatorName>SBB NOVA</siri:OperatorName>
			</ojp:Operator>
		</ojp:Operators>
	</ojp:FareResponseContext>
	<ojp:FareResult>
		<ojp:Id>1231231112231</ojp:Id>
		<ojp:TripFareResult>
			<ojp:FromLegIdRef>1</ojp:FromLegIdRef>
			<ojp:ToLegIdRef>1</ojp:ToLegIdRef>
			<ojp:FareProduct>
				<ojp:FareProductId>mybuxi10001231</ojp:FareProductId>
				<ojp:FareProductName>Kilometerbasiert normal</ojp:FareProductName>
				<ojp:FareAuthorityRef>ch:1:sboid:123123</ojp:FareAuthorityRef>
				<ojp:FareAuthorityText>Mybuxi</ojp:FareAuthorityText>
				<ojp:NetPrice>15</ojp:NetPrice>
				<ojp:Currency>CHF</ojp:Currency>
				<ojp:TravelClass>economy</ojp:TravelClass>
				<ojp:SaleUrl>
					<ojp:Label>
						<ojp:Text>MyBuxi</ojp:Text>
					</ojp:Label>
					<ojp:Url>https://www.mybuxi.ch/booking</ojp:Url>
				</ojp:SaleUrl>
				<ojp:BookingArrangements>
					<ojp:BookingArrangement>
						<ojp:BuyWhen>
							<ojp:PurchaeMoment>onReservation</ojp:PurchaeMoment>
						</ojp:BuyWhen>
						<ojp:MinimumBookingPeriod>PT10M</ojp:MinimumBookingPeriod>
					</ojp:BookingArrangement>
				</ojp:BookingArrangements>
			</ojp:FareProduct>
		</ojp:TripFareResult>
	</ojp:FareResult>
	<ojp:FareResult>
		<ojp:Id>123123123</ojp:Id>
		<ojp:TripFareResult>
			<ojp:FromLegIdRef>2</ojp:FromLegIdRef>
			<ojp:ToLegIdRef>2</ojp:ToLegIdRef>
			<ojp:FareProduct>
				<ojp:FareProductId>öV</ojp:FareProductId>
				<ojp:FareProductName>ÖV</ojp:FareProductName>
				<ojp:FareAuthorityRef>ch:1:sboid:11</ojp:FareAuthorityRef>
				<ojp:FareAuthorityText>NOVA</ojp:FareAuthorityText>
				<ojp:NetPrice>51</ojp:NetPrice>
				<ojp:Currency>CHF</ojp:Currency>
				<ojp:TravelClass>second</ojp:TravelClass>
				<ojp:SaleUrl>
					<ojp:Label>
						<ojp:Text>NOVA</ojp:Text>
					</ojp:Label>
					<ojp:Url>https://www.sbb.ch/NOVA</ojp:Url>
				</ojp:SaleUrl>
			</ojp:FareProduct>
			<ojp:FareProduct>
				<ojp:FareProductId>öV</ojp:FareProductId>
				<ojp:FareProductName>ÖV</ojp:FareProductName>
				<ojp:FareAuthorityRef>ch:1:sboid:11</ojp:FareAuthorityRef>
				<ojp:FareAuthorityText>NOVA</ojp:FareAuthorityText>
				<ojp:NetPrice>75</ojp:NetPrice>
				<ojp:Currency>CHF</ojp:Currency>
				<ojp:TravelClass>first</ojp:TravelClass>
				<ojp:SaleUrl>
					<ojp:Label>
						<ojp:Text>NOVA</ojp:Text>
					</ojp:Label>
					<ojp:Url>https://www.sbb.ch/NOVA</ojp:Url>
				</ojp:SaleUrl>
			</ojp:FareProduct>			
		</ojp:TripFareResult>		
	</ojp:FareResult>

</ojp:OJPFareDelivery>

```
