# HDF5 File Format #


<br>
<h2>Introduction</h2>
MANTIS HDF5 file format follows the Scientific Data Exchange, a set of guidelines for writing scientific data and meta-data in Hierarchical Data Format 5 (HDF5) files developed by APS <a href='http://www.aps.anl.gov/DataExchange/'>http://www.aps.anl.gov/DataExchange/</a>.<br>
<br>
<br>
<h2>HDF5 File Format Version 1.0</h2>
The Data Exchange currently defines a general information and exchange group and three technique specific groups:<br>
<ul><li><font color='red'>information</font>
</li><li><font color='red'>exchange</font>
Technique Specific:<br>
<ul><li><font color='red'>fluorescence</font>
</li><li><font color='red'>spectromicroscopy</font>
</li><li><font color='red'>tomography</font>
Moreover, a data exchange file must contain the scalar string datasets <b>implements</b> and <b>version</b> in the root of the HDF5 file. <b>implements</b> is a colon separated list that shows which components are present in your file. <b>version</b> identifies the data exchange version in use.</li></ul></li></ul>

<table><thead><th> </th><th> <b>Example Data</b>  </th><th> <b>Comments</b> </th></thead><tbody>
<tr><td> <font color='blue'>implements</font> {string} </td><td> "information:exchange:tomography" </td><td>  </td></tr>
<tr><td> <font color='blue'>version</font> {string} </td><td> "1.0" </td><td>  </td></tr></tbody></table>

<br>
<h2>Mantis implementation of the Scientific Data Exchange HDF5 File Format Version 1.0</h2>
Mantis uses the following implementation of the Scientific Data Exchange HDF5 File Format:<br>
<br>
Root of the HDF5 file:<br>
<table><thead><th> <b>Root definition</b> </th><th> <b>Example Data</b>  </th><th> <b>Comments</b> </th></thead><tbody>
<tr><td> <font color='blue'>implements</font> {string} </td><td> "information:exchange:spectromicroscopy" </td><td>  </td></tr>
<tr><td> <font color='blue'>version</font> {string} </td><td> "1.0" </td><td>  </td></tr>
<tr><td> <font color='red'>information/</font> {HDF5 group} </td><td>  </td><td>  </td></tr>
<tr><td> <font color='red'>exchange/</font> {HDF5 group} </td><td>  </td><td>  </td></tr>
<tr><td> <font color='red'>spectromicroscopy/</font> {HDF5 group} </td><td>  </td><td>  </td></tr></tbody></table>

Information HDF5 group definition:<br>
<table><thead><th> <b>Information definition</b></th><th> <b>Example Data</b>  </th><th> <b>Comments</b> </th></thead><tbody>
<tr><td>title {string} </td><td>	"Title" </td><td>  </td></tr>
<tr><td>comment {string} </td><td>	"This is my comment." 	</td><td> Experimenter's comment </td></tr>
<tr><td>file_creation_datetime {string - ISO 8601 format} </td><td>"2011-07-20T15:10Z" </td><td>Date and time the file was created </td></tr>
<tr><td> <font color='red'>ids/</font> {HDF5 group} </td><td>  </td><td>  </td></tr>
<tr><td> :  proposal {string} </td><td>"123456789" </td><td>	APS proposal id</td></tr>
<tr><td>  activity {string} </td><td>"987654321" </td><td>	APS beamline scheduler activity id</td></tr>
<tr><td>   esaf {string} </td><td>	"987654321" </td><td>	ESAF id</td></tr>
<tr><td><font color='red'>experimenter/</font> {HDF5 group} </td><td>  </td><td>  </td></tr>
<tr><td>         name {string} </td><td> 	"John Doe" 	 </td><td>  </td></tr>
<tr><td>         role {string} </td><td>	"Project PI" 	 </td><td>  </td></tr>
<tr><td>           affiliation {string} </td><td>	"University of California, Berkeley" </td><td>  </td></tr>
<tr><td>           address {string} </td><td> "EPS / UC Berkeley CA  94720-4767 USA" </td><td>  </td></tr>
<tr><td>           phone {string} </td><td>	"+1 123 456 0000" </td><td>  </td></tr>
<tr><td>           email {string} </td><td>	"johndoe@berkeley.edu" 	 </td><td>  </td></tr>
<tr><td>           facility_user_id {string}</td><td>"a123456" </td><td>  </td></tr>
<tr><td><font color='red'>sample/</font> {HDF5 group} 	  </td><td>  </td><td>  </td></tr>
<tr><td>           name {string} </td><td>	"cells sample 1" </td><td>  </td></tr>
<tr><td>           description {string} </td><td>	"malaria cells" </td><td>  </td></tr>
<tr><td>           preparation_datetime {string - ISO 8601 format} </td><td>	"2011-07-15T15:10Z" </td><td>	Or optionally, 2011-07-15T15:10+8 for local time, ISO 8601 format.</td></tr>
<tr><td>           chemical_formula {string - abbreviated CIF format} </td><td>	"none" 	 </td><td>  </td></tr>
<tr><td>           environment {string} </td><td>	"air" </td><td>  </td></tr>
<tr><td>           temperature {double} </td><td>	25.4 	 </td><td>  </td></tr>
<tr><td>                @units {string} </td><td>	"C" 	 </td><td>  </td></tr>
<tr><td>           pressure {double} </td><td>	101325 	 </td><td>  </td></tr>
<tr><td>                @units {string} </td><td>	"Pa" 	</td><td>  </td></tr>
<tr><td>           position {string} 	</td><td>"2 D" </td><td>	Position in sample changer. It's in row column format for the APS tomography beamline. Other beamlines may use other formats.</td></tr>
<tr><td> <font color='red'>objective/</font> {HDF5 group} </td><td>  </td><td>  </td></tr>
<tr><td>           manufacturer {string} </td><td>	"Zeiss" </td><td>  </td></tr>
<tr><td>           model {string} </td><td>	"Tube Lens" 	</td><td>  </td></tr>
<tr><td>           comment {string} </td><td>	"1.33 um/pixel" </td><td>  </td></tr>
<tr><td>           magnification {double} </td><td>	5 </td><td> 	 </td></tr>
<tr><td> <font color='red'>scintillator/</font> {HDF5 group} </td><td> 	</td><td>  </td></tr>
<tr><td>           name {string} </td><td>	"Cdw" 	 </td><td>  </td></tr>
<tr><td>           type {string} </td><td>	"Cdw" 	 </td><td>  </td></tr>
<tr><td>           comment {string} </td><td>	"Polished" </td><td>  </td></tr>
<tr><td>           scintillating_thickness {double} </td><td>	300 	 </td><td>  </td></tr>
<tr><td>                @units {string} </td><td>	"um" 	 </td><td>  </td></tr>
<tr><td>           substrate_thickness {double} </td><td>	300 	 </td><td>  </td></tr>
<tr><td>                @units {string} </td><td>	"um" 	 </td><td>  </td></tr>
<tr><td> <font color='red'>facility/</font> {HDF5 group} 	  	 </td><td>  </td><td>  </td></tr>
<tr><td>           name {string} </td><td>	"SLS {APS} Tomcat" 	</td><td>  </td></tr>
<tr><td>           beamline {string}</td><td> 	"Tomcat {2-BM}" </td><td>  </td></tr>
<tr><td>  <font color='red'>accelerator/</font> {HDF5 group} </td><td> 	</td><td>   </td></tr>
<tr><td>           ring_current {double} </td><td>	401.096 </td><td>  </td></tr>
<tr><td>                @units {string attribute - in UDUNITS format} </td><td>	"mA" </td><td>  </td></tr>
<tr><td>           primary_beam_energy {double} </td><td>	19.260 	 </td><td>  </td></tr>
<tr><td>                @units {string} </td><td>	"keV" 	 </td><td>  </td></tr>
<tr><td>           monostripe {string} </td><td>	"Ru/C" 	 </td><td>  </td></tr>
<tr><td>  <font color='red'>detector/</font> {HDF5 group} 	  	</td><td>  </td><td>  </td></tr>
<tr><td>          manufacturer {string} </td><td>	"CooKe Corporation" 	 </td><td>  </td></tr>
<tr><td>           model {string} </td><td>	"pco.dimax" 	 </td><td>  </td></tr>
<tr><td>           serial_number {string} </td><td>	"1234XW2" </td><td>  </td></tr>
<tr><td>          bit_depth {integer} </td><td>	12 </td><td> 	 </td></tr>
<tr><td>           operating_temperature {double} </td><td> 	-29.5 	 </td><td>  </td></tr>
<tr><td>                @units{string} </td><td> 	"C" </td><td> 	 </td></tr>
<tr><td>           exposure_time {double} </td><td> 	170 </td><td> 	</td></tr>
<tr><td>                @units{string} </td><td> 	"ms" </td><td>  </td></tr>
<tr><td>           frame_rate {integer} </td><td>	2 </td><td>	Frames per second (fps)</td></tr>
<tr><td>  <font color='red'>pixel_size/</font> {HDF5 group }	 </td><td>  	 </td><td>  </td></tr>
<tr><td>                horizontal {double} </td><td>	0.7 	</td><td>  </td></tr>
<tr><td>                     @units {string} </td><td>	"um" 	 </td><td>  </td></tr>
<tr><td>                vertical {double} </td><td>	0.7 </td><td> 	 </td></tr>
<tr><td>                     @units {string} </td><td>	"um" </td><td>  </td></tr>
<tr><td>   <font color='red'>dimensions/</font> {HDF5 group }	  </td><td>  </td><td>  </td></tr>
<tr><td>                horizontal {integer}</td><td> 	2048 </td><td>	Number of pixels wide</td></tr>
<tr><td>                vertical {integer} </td><td>	1024 </td><td>	Number of pixels high</td></tr>
<tr><td>   <font color='red'>binning/</font> {HDF5 group }	  </td><td>  </td><td>  </td></tr>
<tr><td>                horizontal {integer}</td><td> 	2 </td><td>	Horizontal binning factor</td></tr>
<tr><td>                vertical {integer} </td><td>	1 </td><td>	Vertical binning factor</td></tr>
<tr><td>    <font color='red'>axis_directions/</font> {HDF5 group }</td><td> 	</td><td>  </td></tr>
<tr><td>                horizontal {string} </td><td>	"X+" </td><td>	 </td></tr>
<tr><td>                vertical {string} </td><td>	"Y+" 	 </td><td>  </td></tr>
<tr><td> <font color='red'>roi/</font> {HDF5 group }	 </td><td>  </td><td>  </td></tr>
<tr><td>                x1 {integer} </td><td>	256 </td><td>	"Left pixel position"</td></tr>
<tr><td>                y1 {integer}</td><td> 	256 </td><td>	"Top pixel position"</td></tr>
<tr><td>               x2 {integer} </td><td>	1792 </td><td>	"Right pixel position"</td></tr>
<tr><td>                y2 {integer}</td><td> 	1792 </td><td>	"Bottom pixel position" </td></tr></tbody></table>

Exchange HDF5 group definition:<br>
<table><thead><th> <b>Exchange definition</b></th><th> <b>Example Data</b>  </th><th> <b>Comments</b> </th></thead><tbody>
<tr><td><font color='red'>exchange/</font>{HDF5 group}   </td><td>  </td><td>  </td></tr>
<tr><td>      title {string} </td><td> 	"Title" 	</td><td>  </td></tr>
<tr><td>      comment {string} 	  </td><td>  </td><td>  </td></tr>
<tr><td>      data_collection_datetime {string} </td><td> 	"2011-07-15T15:10Z" 	 </td><td>  </td></tr>
<tr><td>      data </td><td>  </td><td>  n-dimensional dataset 	</td></tr>
<tr><td>           @signal {integer attribute}</td><td>  	1 </td><td>  </td></tr>
<tr><td>           @description {string attribute}</td><td>  	"transmission"</td><td>  </td></tr>
<tr><td>            @units {string attribute} 	 </td><td>  </td><td>  	See Detector Signal Description Types.</td></tr>
<tr><td>           @axes {string attribute} 	</td><td> "z:y:x" 	</td><td> Colon separated list of dimension names; see coordinate naming conventions.</td></tr>
<tr><td>           @detector {string attribute}  </td><td> 	"/information/detector" </td><td> 	Entry for detector used to collect this data.</td></tr>
<tr><td>      x </td><td>  </td><td> {dimension scale for dimension 2 of the data; could be x, theta_x, etc.} 	</td></tr>
<tr><td>         @units {string} </td><td>  </td><td>  </td></tr>
<tr><td>      y</td><td>  </td><td>  {dimension scale for dimension 2 of the data; could be y or theta_y, etc.} 	  </td></tr>
<tr><td>           @units {string} 	 </td><td>  </td><td>	 </td></tr>
<tr><td>      z </td><td>  </td><td> {dimension scale for dimension 0 of the data; could be z or theta_z, etc.} 	</td></tr>
<tr><td>           @units {string} 	  	</td><td>  </td><td>  </td></tr>
<tr><td>      energy</td><td>  </td><td>  {1 dimensional array of energy values corresponding to z-axis} 	  	 </td></tr>
<tr><td>           @units {string} 	  </td><td>  </td><td>  </td></tr>
<tr><td>       white_data </td><td>  </td><td> {3 dimensional array or normalization factors} 	  	 </td></tr>
<tr><td>           @units {string} 	</td><td>  </td><td>  </td></tr>
<tr><td>      dark_data </td><td>  </td><td> {3 dimensional array} 	  	 </td></tr>
<tr><td>           @units {string} 	 </td><td>  </td><td>  </td></tr>
<tr><td>      rotation {double} </td><td>  </td><td> 	90.0 	Data rotation in degrees. </td></tr></tbody></table>

Spectromicroscopy HDF5 group definition:<br>
<table><thead><th> <b>Spectromicroscopy definition</b></th><th> <b>Example Data</b>  </th><th> <b>Comments</b> </th></thead><tbody>
<tr><td><font color='red'>spectromicroscopy/</font>{HDF5 group}   </td><td>  </td><td>  </td></tr>
<tr><td> positions </td><td>  </td><td> {n-dimensional float array} 	</td></tr>
<tr><td> @units {string} </td><td>  	3 dimensional array of actual pixel positions in microns in x direction 	Used for arbitrary scan shapes (like a circle)</td><td>  </td></tr>
<tr><td>  @names {string}</td><td>  	"microns" 	 </td><td>  </td></tr>
<tr><td>  <font color='red'>normalization/</font>{ {HDF5 group} 	</td><td>  </td><td>  </td></tr>
<tr><td>  white_spectrum {1 dimensional array} 	 </td><td>  </td><td>  	Io normalization spectrum </td></tr>
<tr><td>  @units {string} 	  </td><td>  </td><td>  </td></tr>
<tr><td>  white_spectrum_energy {1 dimensional array} </td><td>  </td><td> 	"eV" 	Io energies </td></tr>
<tr><td>  @units {string} 	  	 </td><td>  </td><td>  </td></tr></tbody></table>

Note: @ denotes HDF5 attributes.<br>
