# Mantis User Guide #



## Table of Contents ##


<br>
<h2>Introduction</h2>
MANTiS is <b>M</b>ultivariate <b>AN</b>alysis <b>T</b>ool for <b>S</b>pectromicroscopy developed in Python. It uses Principal Component Analysis (PCA) and cluster analysis to classify pixels according to spectral similarity, as well as singular value decomposition for calculating image thicknesses based on known<br>
spectra; the addition of Non-Negative Matrix Approximation or NNMA-based analysis is in development The program is based on the earlier programs <a href='http://dx.doi.org/10.1046/j.1365-2818.2000.00640.x'>stack_analyze</a> and <a href='http://dx.doi.org/10.1016/j.ultramic.2004.01.008'>pca_gui</a> in the <a href='http://www.exelisvis.com/ProductsServices/IDL.aspx'>IDL</a> programming language by Mirna Lerotic and Chris Jacobsen of the Stony Brook University x-ray microscopy group (the Stony Brook<br>
group moved to <a href='http://xrm.phys.northwestern.edu/'>Northwestern University</a> in 2010). Initial development of MANTiS was supported by Argonne National Laboratory under LDRD project 2010-193-<a href='https://code.google.com/p/spectromicroscopy/source/detail?r=1'>R1</a> 9113, with further enhancements supported under LDRD 2013-168-N0.<br>
<br>
The <a href='http://dx.doi.org/10.1016/j.ultramic.2004.01.008'>paper</a> of Lerotic <i>et al.</i>, <i>Ultramicroscopy</i> <b>100</b>, 35-57 (2004), provides the mathematical description. In experiments one measures a set of images over <i>N</i> energies and <i>P</i> pixels, leading to a data matrix <i>D</i><sub><i>N</i> x <i>P</i></sub>.  One would like to describe this as a product of a set of spectra <i>u</i><sub><i>N</i> x <i>S</i></sub> and a set of thickness or weighting maps <i>t</i><sub><i>S</i> x <i>P</i></sub>; in this case <i>S</i> indexes a set of spectroscopically distinct components that one would like to discover.  If one already knows a set of spectra for all chemical components in the sample, the matrix <i>u</i><sub><i>N</i> x <i>S</i></sub> is already determined and one can simply find the thickness maps with <i>t</i><sub><i>S</i> x <i>P</i></sub>=(<i>u</i><sub><i>N</i> x <i>S</i></sub>)<sup>-1</sup>.<i>D</i><sub><i>N</i> x <i>P</i></sub>. When one does not know all of the spectra  <i>u</i><sub><i>N</i> x <i>S</i></sub> in advance, MANTiS provides a way to discover these spectra from correlations in the data.<br>
<br>
MANTiS presents the user with a sequence of panels to guide the analysis workflow.  This sequence is illustrated in the following schematic:<br>
<br>
<table width='630'>
<tr><td><img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/workflow_schematic.png' /></td></tr>
<tr><td><font size='1'><b>Figure 0</b> Workflow Schematic </font></td></tr>
</table>
<br>

<br>
<h2>Installation</h2>
Mantis python source code can be downloaded here <a href='http://goo.gl/OnGr3p'>Download Mantis</a>. Alternatively, a local copy of the spectromicroscopy <a href='http://mercurial.selenic.com/'>Mercurial</a> repository can be made with this command:<br>
<pre><code>hg clone https://code.google.com/p/spectromicroscopy/  <br>
</code></pre>

The program requires Python 2.7 and following dependencies<br>
<ul><li><a href='http://www.riverbankcomputing.com/software/pyqt/'>PyQt</a> (for newer versions)<br>
</li><li><a href='http://www.wxpython.org/download.php#stable'>wxPython</a> (for older versions)<br>
</li><li><a href='http://numpy.scipy.org/'>Numpy</a>
</li><li><a href='http://numpy.scipy.org/'>Scipy</a>
</li><li><a href='http://matplotlib.sourceforge.net/'>Matplotlib</a>
</li><li><a href='http://code.google.com/p/h5py/'>h5py</a>
</li><li>(Optional) <a href='http://code.google.com/p/netcdf4-python/'>netcdf4-python</a>
</li><li>(Optional) <a href='http://www.pythonware.com/products/pil/'>Python Imaging Library (PIL)</a></li></ul>

We recommend the Enthought Python Distribution <a href='http://www.enthought.com/products/epd.php'>EPD</a> which includes all the required libraries in compatible versions. <a href='http://docs.continuum.io/anaconda/'>Anaconda Python</a> is another option.<br>
<br>
<br>
<br>
<h2>Processing your data</h2>
To carry out your analysis via PCA and clustering (see the above figure), you need to go through the following steps:<br>
<ol><li>Loading your data<br>
</li><li>Preprocessing: alignment, normalization to optical density, removal of artifact images<br>
</li><li>PCA (principal component analysis): orthogonalization of spectra (generation of eigenspectra), and selection of <i>S</i> principal components (removal of noisy eigenspectra)<br>
</li><li>Cluster analysis: grouping of spectroscopically similar pixels together to find a representative set <i>S</i> of spectra (<i>i.e.</i>, to discover <i>u</i><sub><i>N</i> x <i>S</i></sub>)<br>
</li><li>Spectral Maps: calculation of thickness or weighting maps <i>t</i><sub><i>S</i> x <i>P</i></sub> from <i>t</i><sub><i>S</i> x <i>P</i></sub>=(<i>u</i><sub><i>N</i> x <i>S</i></sub>)<sup>-1</sup>.<i>D</i><sub><i>N</i> x <i>P</i></sub>.  This step is also known as target mapping: you obtain maps of the distribution of a set of target spectra.<br>
These steps are described in turn below.<br>
# Peak IDs: once you have a set of spectra <i>u</i><sub><i>N</i> x <i>S</i></sub> you can either load or build a list of names and energies of functional group peaks you expect to find in the spectra (e.g., C=C aromatic bonds at 285.0 eV).<br>
# Peak Fitting: here you can fit a particular spectrum <i>u</i><sub><i>N</i> x <i>s</i></sub> for a particular component <i>s</i> to a set of near-edge absorption resonances and an absorption edge step.</li></ol>

<h3>Reading your data</h3>
The first step in using Mantis is to read in a set of images over known photon energies (a <i>stack</i>).<br>
<br>
<table width='630'>
<tr><td><img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss00.png' /></td></tr>
<tr><td><font size='1'><b>Figure 1</b> Load Data Tab. Loaded images can be viewed using the scroll-bar and arrows on Windows and Linux; on Mac the arrows are no longer available but the same functionality can be imitated by clicking on the scroll-bar and using arrow keys on the keyboard. </font></td></tr>
</table>
<br>


To read in a pre-organized stack of files, use the <img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/mantis_openfile.png' /> <b>read stack</b> icon. At present, Mantis is able to read stacks in the following formats:<br>
<ul><li><code>.hdf5</code> or <code>.h5</code> <a href='http://www.hdfgroup.org/HDF5/'>HDF5</a> files organized in the <a href='http://www.aps.anl.gov/DataExchange/'>DataExchange</a> schema developed at Argonne National Laboratory.<br>
</li><li><code>.hdr</code> self-describing files (SDF) initially developed for the ALS Scanning Transmission X-Ray Microscope (STXM) at beamline 5.3.2. (used by ALS and SLS).<br>
</li><li><code>.stk</code> <a href='http://en.wikipedia.org/wiki/External_Data_Representation'>XDR</a> files as created by the  <a href='http://dx.doi.org/10.1046/j.1365-2818.2000.00640.x'>`stack_analyze`</a> IDL program developed by the x-ray microscopy group at Stony Brook University.<br>
</li></ul><ul><li><code>.xrm</code> OLE Compound single energy files. The file format was developed by <a href='http://www.xradia.com/'>Xradia</a>.<br>
</li><li><code>.txrm</code> OLE Compound files. The file format was developed by <a href='http://www.xradia.com/'>Xradia</a>.<br>
</li><li><code>.tif</code> Multi-Page TIFF 8 and 16 bit files. A list of corresponding energies (in eV) has to be stored in a .txt file with the same file name as the data (e.g., mydata.tiff and mydata.txt). Multi-Page .tiff file can be created from individual .tiff images using <a href='http://www.irfanview.com'>IrfanView</a>.<br>
More formats can be accommodated by request.</li></ul>

To build a stack from a set of individual images, use the <img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/mantis_opendirectory.png' /> <b>open directory</b> icon.  This will allow you to read in a sequence of files. Supported single energy file formats are<br>
<ul><li><code>.sm</code> <a href='http://www.unidata.ucar.edu/software/netcdf/'>NetCDF</a> files written by the soft x-ray scanning transmission x-ray microscope developed by Stony Brook University and formerly operated at beamline X1A at NSLS at Brookhaven; and<br>
</li><li>.xrm OLE Compound single energy files, in a format developed by <a href='http://www.xradia.com/'>Xradia</a>.</li></ul>

Again, more image file types can be accommodated by request.  This is illustrated in Fig. 2.<br>
<br>
<table width='410'>
<tr><td><img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss02.png' /></td></tr>
<tr><td><font size='1'><b>Figure 2</b> The <b>open directory</b> widget is used to build an image stack from a list of files (in this case, a set of  <code>.sm</code> <a href='http://www.unidata.ucar.edu/software/netcdf/'>NetCDF</a> files written by the soft x-ray scanning transmission x-ray microscope developed by Stony Brook University and formerly operated at beamline X1A at NSLS at Brookhaven). You pick a starting and ending file, and then these files (plus all in between) are used to create a list of files that belong to a stack. </font></td></tr>
</table>
<br>

<h3>Preprocessing of your data: the Image Stack tab</h3>
Once you have read in a sequence of files, you have an assembled stack for examination and preprocessing. A suggested workflow is as follows:<br>
<ol><li>Align your image sequence.<br>
</li><li>Normalize your images to obtain the optical density (in the case of transmission imaging).</li></ol>

<table width='630'>
<tr><td><img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss01.png' /></td></tr>
<tr><td><font size='1'><b>Figure 3</b> Preprocess Tab </font></td></tr>
</table>
<br>

<h4>Image Alignment</h4>
Acquiring images at successive energies often requires that a focusing stage be adjusted, and these stages often have a runout error which causes the center position of the image to shift.  One must therefore align the stack to eliminate image-to-image wobble; this is done using the <b>Align images</b> button which brings up the window shown in Fig. 4.<br>
<br>
The <b>Registration</b> or automatic alignment routine attempts to align images by Fourier cross-correlation.  The first step is to drag the slider to the right of the left-hand image to select a photon energy with an image that is sharp and with good contrast; you then click on <b>Set as Reference Image</b>.  You then click on the <b>Register Images</b> button to start the automatic alignment procedure; if you have checked the <b>Show Cross-correlation</b> box you'll see how the calculation proceeds (at a cost in speed due to the need to display a large number of images).  The program goes through and calculates the <b>Image shifts</b> in pixels between successive images and the reference image.  At the end of this process, you should click on <b>Crop Aligned Images</b> to trim the edges that are present in only a subset of the image sequence.<br>
<br>
Sometimes the automatic alignment routine just cannot seem to get a stack to align; for example, there might be similar features present at different locations in the image and the alignment routine might suddenly align feature A in image 1 with feature B in image 2. You may then want to resort to <b>Manual Alignment</b>. In this procedure, you are told first to <b>Pick a point on reference image</b>, where you should use a readily-identified fine feature in the reference image. You will then be asked to <b>Pick a corresponding point</b> or to click on this same feature in each of the images in the stack.  When you're done, you must click on <b>Apply manual shifts</b> to apply the new alignment to the stack.<br>
<br>
<b>Crop Aligned Images</b> will crop the images to a region of pixels that are present in every single image in the stack. That is, as images have been shifted around in alignment, there may be some features that appear at the edges of some images but fall outside the field of view of other images. These edges which are not present in all of the files are clipped out.<br>
<br>
<b>Remove Image</b> will remove the displayed image from the stack.<br>
<br>
<b>Accept changes</b> will apply the registration shifts to the stack and return to the main frame.<br>
<br>
<table width='630'>
<tr><td><img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss05.png' /></td></tr>
<tr><td><font size='1'><b>Figure 4</b> The image alignment window.</font></td></tr>
</table>


<br>

<h4>Image Normalization</h4>
Once your images are aligned, you can now thing about normalization of the images in the <b>Image Stack</b> window. Often in experiments the transmitted flux <i>I</i> is recorded, but this decays exponentially with sample thickness <i>t</i> as <i>I</i>=<i>I</i><sub>0</sub>exp(-<i>ut</i>), where <i>u</i> is the absorption coefficient (the same <i>u</i> as obtained from a specific photon energy <i>n</i> and chemical component <i>s</i> from <i>u</i><sub><i>N</i> x <i>S</i></sub>).  For spectromicroscopy analysis, it is preferable to work instead with the optical density <i>D</i>=-log(<i>I</i>/<i>I</i><sub>0</sub>)=<i>ut</i> which is linear with specimen thickness.  To calculate the optical density matrix <i>D</i><sub><i>N</i> x <i>P</i></sub>, you need to obtain the incident flux spectrum <i>I</i><sub>0</sub>(<i>N</i>).  This can be obtained from a blank area in the image stack, or from a separate measurement; both can be determined from within the <b>Preprocess Data</b> tab in Mantis as shown in Fig. 2.<br>
<br>
<br>
<br>
<h3>Evaluating your images: the Preprocess Data tab</h3>
The <b>Preprocess Data</b> tab displays the page shown in Fig. 2. After the image stack has been loaded, an image and a spectrum will be displayed. One can view images acquired at different photon energies using either the slidebar to the right of the image, or by clicking on the spectrum at the desired energy. One can also change the pixel from which the spectrum is displayed by clicking at points on the image.  If the data is normalized, you can chose to display either the transmitted flux <i>I</i> or the optical density <i>D</i>.<br>
<br>
<br>
<h4>Preprocess tab</h4>
There are two methods for determining the incident flux spectrum <i>I</i><sub>0</sub>(<i>N</i>):<br>
<ul><li><b>I0 from file</b> reads in an <i>I</i><sub>0</sub>(<i>N</i>) spectrum file, in case you have it in a separate measurement. The spectrum should be saved in a simple ASCII format of (eV, flux in kHz) stored line-by-line, such as a <code>.csv</code> file (comment lines that start with text or non-numeric symbols will be ignored). You should make sure that this file was recorded under similar conditions, like the same slit settings, ring current, and so on.<br>
</li><li><b>I0 from histogram</b>  is used to determine the <i>I</i><sub>0</sub> spectrum from the histogram of image intensities.  This is described in the caption of Fig. 5.</li></ul>

<table width='630'>
<tr><td><img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss03.png' /></td></tr>
<tr><td><font size='1'><b>Figure 5</b> Clicking on <b>I0 from histogram</b> produces this window, which displays a histogram of image intensities (top).  One can then obtain a <i>I</i><sub>0</sub>(<i>N</i>) spectrum by picking the pixels with the greatest average signal level, such as the pixels corresponding to a whole in the image.  By clicking and dragging over an energy range, one obtains a red box indicating the flux range used for the <i>I</i><sub>0</sub>(<i>N</i>) spectrum in the histogram at top, and these pixels are highlighted in red in the image below. </font></td></tr>
</table>
<br>

<b>Show I0</b> displays the <i>I</i><sub>0</sub>(<i>N</i>) normalization spectrum.<br>
<br>
<b>Limit energy range</b> button brings up a frame shown on Fig. 6. This allows one to exclude images outside of a defined energy range from subsequent analysis.  Sometimes the best results are obtained by limiting cluster analysis to the energy range of the near-edge absorption "wiggles" only, without the extended pre-edge and post-edge spectral regions that are otherwise useful for absorption calibration.<br>
<br>
<b>Save</b> button displays a frame where different things can be saved as .png, .pdf, or .svg files.   The .pdf and .svg files use vector graphics and font-based text, so you can use a program like Adobe Illustrator or Inkscape to tweak the graphics output files.<br>
<br>
<table width='630'>
<tr><td>
<img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss04.png' />
</td></tr>
<tr><td>
<font size='1'><b>Figure 6</b> The <b>Limit energy range</b> window of Mantis.  By dragging the mouse across an energy region, one can define a limited range used for subsequent spectromicroscopy analysis.</font>
</td></tr>
</table>

<br>

<br>
<h4>Display settings</h4>
<b>Despike</b> When using a gas-filled proportional counter, sometimes you can get single pixels with very large values (one or a few bright white dots) due to electrical discharges, cosmic rays hitting the detector, and so on. This option looks for such pixels and sets them to the average of their neighboring pixels for all images in the stack.<br>
<br>
You can choose to display either the transmitted flux or the optical density OD = −log (I/I0).<br>
You can adjust the contrast of the image by playing with Minimum, Maximum, and Gamma. The image is displayed as<br>
<br>
<img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/optical_density.png' />

The program starts out displaying the full range of the data at a contrast of gamma=1.<br>
<br>
<b>Color Table</b> is used to adjust the colormap used to display the image  Colors button.<br>
<br>
<br>
<h4>Region of Interest</h4>
Spatial regions of interest (ROIs) are sets of pixels from which one averages the individual spectra to obtain an ROI spectrum.<br>
<b>Add ROI</b> will enable you to start defining a new Region of Interest (ROI) as shown on Fig. 6. After clicking on <b>Add ROI</b> you can then define a region bounded by a polygon by clicking with the left mouse button to define points. If a ROI is already present and accepted, the new region will be added to the old one.<br>
<br>
<b>Accept ROI</b> will take the outline you made, make a ROI, and show the spectrum obtained by averaging the per-pixel spectra within this area.<br>
<br>
<b>Reset ROI</b> will reset ROIs and return to regular use (showing single pixel spectra)<br>
<br>
<b>Set ROI as I0</b> will used the raw data in the region as a normalization spectrum I0.<br>
<br>
<b>Save ROI Spectrum</b> will save the ROI spectrum as a .xas file, which is a plain text file of ev, signal per line (e.g., Microsoft Excel can read it as a .csv or comma space value file).<br>
<br>
<img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss10.png' />
<font size='1'><b>Figure 7</b> Selecting a Region of Interest.</font>
<br>

<b>Spectral ROI</b> lets you can average a number of images in the pre-edge spectral region for a single I0 image, and a number of images in a post-edge spectral region for a single I image (you can also use just one file for each spectral region). This lets you get a very high signal-to-noise ratio for making a map or image of the optical density of a selected element (for example by using several images below and then above in energy the Carbon absorption edge). The panel is shown on Fig. 8.<br>
<br>
<table width='630'>
<tr><td>
<img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss11.png' />
</td></tr>
<tr><td>
<font size='1'><b>Figure 8</b> Spectral ROI is used to calculate an element map.   Pre-absorption-edge and post-edge regions are slected by directing a user to by dragging the mouse across an energy regions.</font>
</td></tr>
</table>
<br>


<br>
<h3>PCA tab</h3>

Principal Component Analysis (PCA) tab is used to calculate eigenspectra and eigenimages from the image stack. The eigenspectra are an orthogonal set of spectra that, in linear combination, can describe any observed spectrum in the dataset.  The eigenvalue associated with each eigenspectrum gives a typical weighting of that eigenspectrum in the entire data set, so that the later eigenspectra (with small eigenvalues) represent less and less common variations in the data.  For this reason it is often possible to have an excellent approximation of all observed spectra using a linear combination of only a limited set of eigenspectra (the principal components); finding this limited set is what PCA is all about.<br>
<br>
Because optical density OD is used for the calculation it is necessary to define a normalization spectrum I0. If the data was normalized beforehand and a normalization spectrum is not required, a .xas file with flux values of -1.0 can be loaded.<br>
<br>
If the image stack and I0 spectrum are both loaded, the <b>Calculate PCA</b> button of Fig. 9 becomes sensitive. The button launches the calculation of principal components.<br>
<br>
The PCA tab has three graphic areas which display eigenimages, eigenspectra, and eigenvalues. Eigenvalues are depicted in the plot on upper right corner. The current eigenimage is shown on the lower left corner in a blue-red intensity scale. The scale is such that negative values are scaled red, positive values blue, and zero is white. The current eigenspectrum is shown in the lower left corner. The slider to the right of the eigenimage plot is used to select which eigenimage and eigenvalue are shown.<br>
<br>
Your goal in examining eigenspectra and eigenimages is to select the number of significant components present in the sample. You can generally tell when components become insigificant by observing a “salt and pepper” pattern in eigenimages (indicating that the component is showing only random rather than systematic variations of signal from pixel-to-pixel), and random fluctuations in the eigenspectra. Based on this examination, you should adjust the <b>Number of Significant Components</b> to be used in further analyses of the data. Initial estimation of the number of significant components is calculated using  <a href='http://www.tandfonline.com/doi/abs/10.1207/s15327906mbr0102_10'>the Cattell scree test</a>, where the number of significant components is given by detecting an "elbow" in the eigenvalue plot. Cumulative variance captured by the Significant Components is displayed.<br>
<br>
<br>
<br>
<br>
<b>Save</b> button displays a frame where different things can be saved as .png, .pdf, or .svg files.<br>
<br>
<br>
<img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss06.png' />
<font size='1'><b>Figure 9</b> Principal component analysis tab. After pressing the <b>Calculate PCA</b> button, you must decide the number of significant components based on observations of the eigenvalues, eigenimages, and eigenspectra.</font>


<br>
<h3>Cluster Analysis tab</h3>

The <b>Cluster analysis</b> tab shown in Fig. 10 is used to classify data in the image stack according to spectral similarities. When searching for clusters, you are looking for pixels with similar weightings of eigenspectra. Therefore the <b>Calculate Clusters</b>  button becomes sensitive only after Principal Components have been calculated in the PCA Tab. Before running cluster analysis it is required to choose the <b>Number of clusters</b> to be sought with the  slide-bar in the upper left corner. Generally speaking it’s suggested that you search for several more clusters than the number of components you have identified as significant.<br>
<br>
In some cases, it is also useful to exclude the first component from the cluster search, so as to reduce sensitivity to thickness variations (the first component represents the average absorption spectrum present in the dataset, while subsequent components represent variations from that average in decreasing significance). <b>Reduce thickness effects</b> will preform cluster analysis without the first component.<br>
<br>
While <i>k</i>-means clustering attempts to cluster pixels together based on minimum Euclidian distances in eigenspectrum-weighting space, it sometimes delivers clusters where one or a few pixels with unique spectroscopic signatures are incorrectly grouped with a large number of more self-similar pixels.  Selecting the option of <b>Divide clusters with large Sigma</b> will look for the cluster with the biggest variance of Euclidian distances from the cluster center, and break it into two clusters.<br>
<br>
Once you have decided upon the above settings, click on <b>Calculate Clusters</b> to start the clustering calculation. When the calculation is finished, results are displayed on four graphical areas:<br>
<ol><li>Composite cluster image: A pseudo-color image of cluster pixels for first twelve clusters is generated by displaying members of different clusters with different colors.<br>
</li><li>Cluster <i>i</i>: An image of pixels which are part of the currently-selected cluster is shown in the upper right corner. The index for this cluster can be chosen using the slider to the right of the image.<br>
</li><li>Cluster <i>i</i> spectrum: The spectrum of the current cluster is displayed in the lower right corner.<br>
</li><li>Cluster distance map: Distances from cluster centers provide a way to detect errors in clustering. This image shows scaled distances from pixels to their respective cluster center, with white pixels being the farthest away (i.e., the least similar spectroscopically to the other pixels in their respective cluster).</li></ol>

<b>Show scatter plots</b> button opens the scatter plot widget shown on Fig. 11.<br>
<br>
<b>Save CA results</b> button displays a frame where images, spectra, and/or scatterplots can be saved as .png, .pdf, .svg, or .hdf5 files.<br>
<br>
<img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss07.png' />
<font size='1'><b>Figure 10</b> Cluster analysis tab.</font>



<table width='600'>
<tr><td>
<img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss08.png' />
</td></tr>
<tr><td>
<font size='1'><b>Figure 11</b> The scatter plot shows pixels plotted according to their weights in two selected component axes. Because clustering is done over a number of dimensions equal to the number of selected components, a particular two dimensional view will give an incomplete sense of how the data are clustered.</font>
</td></tr>
</table>


<br>
<h3>Spectral Maps tab</h3>

The Spectral Maps tab shown in Fig. 12 allows one to get thickness or spectral weighting maps <i>t</i><sub><i>S</i> x <i>P</i></sub> from already-known spectra <i>u</i><sub><i>N</i> x <i>S</i></sub>, whether from the cluster spectra (if clustering has been done) or from a different set of spectra loaded into MANTiS.<br>
<br>
Once any spectrum has been loaded, thickness maps are calculated according to <i>t</i><sub><i>S</i> x <i>P</i></sub>=(<i>u</i><sub><i>N</i> x <i>S</i></sub>)<sup>-1</sup>.<i>D</i><sub><i>N</i> x <i>P</i></sub> even for as few as <i>S</i>=1 spectra.  This means that the spectra should have units of linear absorption coefficients in inverse micrometers (the <i>u</i> in exp[-<i>ut</i>]).   If you have multiple spectra to include, simply continue loading the additional spectra; if you choose instead to <b>Add Cluster Spectra</b> you will get the entire set of thickness or spectral weighting maps corresponding to the full set of cluster spectra.<br>
<br>
If you know all spectra, you can get thickness maps using SVD by selecting <b>Load Spectrum</b> to load any known target spectra as linear absorption c (“mu”) in a .xas file format.<br>
<br>
The option <b>Add flat spectrum</b> loads one “flat,” constant-at-all-energies spectrum for evaluating fit residuals.<br>
<br>
<b>Add cluster spectra</b> loads all cluster spectra from the current cluster analysis.<br>
<br>
The list of loaded <b>Target Spectra</b> is shown in the upper right corner.<br>
<br>
<b>Remove spectrum</b> removes the displayed target spectrum. <b>Move<br>
spectrum up</b> moves a displayed spectrum up in the list, while <b>Move spectrum down</b> moves a displayed spectrum down in the list. There is also an option to show maps from raw spectra or spectra fitted with principal components (Composition Map).<br>
<br>
The current target spectrum is shown in the lower left corner. The slider to the right of the  plot is used to select which target spectrum and thickness map are shown. The plot shows raw spectrum as well as spectrum fitting with PCA components and absolute difference spectrum. The fit weights for the current spectrum are displayed under <b>Fit Weights</b>.<br>
<br>
After the spectrum is loaded, the calculated thickness map is shown on the lower left corner in a white-red intensity scale. The scale is such that negative values are scaled red, positive values white, and zero is black.<br>
<br>
<br>
<img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss09.png' />
<font size='1'><b>Figure 12</b> Spectral Analysis tab.</font>

<b>Composite RGB image</b> can be created using the widget shown on Fig 13.<br>
<br>
<br>
<table width='630'>
<tr><td><img src='http://wiki.spectromicroscopy.googlecode.com/hg/images/Mantis_ss12.png' /></td></tr>
<tr><td><font size='1'><b>Figure 13</b> Composite RGB image created from three selected spectra.</font></td></tr>
</table>
<br>

<h2>References</h2>

<ol><li>Lerotic M, Mak R, Wirick S, Meirer F, Jacobsen C. MANTiS: a program for the analysis of X-ray spectromicroscopy data.  J. Synchrotron Rad. 2014 Sep; 21(5); 1206–1212  - <a href='http://dx.doi.org/10.1107/S1600577514013964'>http://doi.org/10.1107/S1600577514013964</a>
</li><li>Mak R, Lerotic M, Fleckenstein H, Vogt S, Wild SM, Leyffer S, Sheynkin Y, Jacobsen C. Non-Negative Matrix Analysis for Effective Feature Extraction in X-Ray Spectromicroscopy. Faraday Discussions. 2014 Apr; 171- <a href='http://doi.org/10.1039/C4FD00023D'>http://doi.org/10.1039/C4FD00023D</a>
</li><li>Lerotic M, Jacobsen C, Gillow JB, Francis AJ, Wirick S, Vogt S, Maser J. Cluster analysis in soft X-ray spectromicroscopy: Finding the patterns in complex specimens. Journal of Electron Spectroscopy and Related Phenomena. 2005 Jun; 144–147, p:1137-1143 - <a href='http://dx.doi.org/10.1016/j.elspec.2005.01.158'>http://dx.doi.org/10.1016/j.elspec.2005.01.158</a>
</li><li>Lerotic M, Jacobsen C, Schäfer T, Vogt S. Cluster analysis of soft X-ray spectromicroscopy data. Ultramicroscopy. 2004 Jul; 100(1–2), p:35-57 - <a href='http://doi.org/10.1016/j.ultramic.2004.01.008'>http://doi.org/10.1016/j.ultramic.2004.01.008</a></li></ol>

Please use the following reference when quoting Mantis<br>
<br>
Lerotic M, Mak R, Wirick S, Meirer F, Jacobsen C. MANTiS: a program for the analysis of X-ray spectromicroscopy data.  J. Synchrotron Rad. 2014 Sep; 21(5); 1206–1212