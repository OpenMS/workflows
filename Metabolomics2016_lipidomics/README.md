##Overview
This folder contains the lipidomics demonstration workflow as presented during the Metabolomics 2016 conference, as well as required database files, adduct list files, etc...

It shows how OpenMS can be used in KNIME to cover various steps in MS lipidomics analyses. Used OpenMS algorithms include:
- detection of lipid features (i.e., m/z, intensity and retention time centroids with their convex peak hulls for a specific lipid ion) in individual samples
- alignment of samples by retention time
- finding of corresponding lipid features across samples
- feature identification via search by accurate mass or spectral matching

The workflow further demonstrates how one can use KNIME nodes for interactive visualization, and how to do statistical analyses (exemplified in a discovery section for compounds with significantly differing intensities) with in this case scripting nodes for the statistical language R.
The use of KNIME nodes for table operations allows to combine results of various subsections to e.g, annotate quantified compounds of interest with putative identifications, or to find features with probable MS/MS matches and suitable precursor information.
If chromatographic information about lipid behaviour is available in sufficient detail, it is also sensible to think about using retention time information for filtering. Our demonstration workflow provides a subsection dedicated to filtering mass-based ID candidates via errors between experimental and predicted retention times. The section in question has previously been shown to achieve very good filtering results for an analysed dataset.

Of course, there rarely exists a one-size-fits-all solution pipeline. This is especially true for the still developing field of metabolomics. Here, oftentimes each study requires its own custom-tailored analysis pipeline. In this light, our workflow could be considered more of a starting point or initial template to build upon for your individualized lipidomics analyses. We hope this demonstration workflow could illustrate some of the capabilities of OpenMS in KNIME, and convince you of its modularity and adaptability.


##How to use - Input files
To download a specific file, please click on it and then click on the 'Raw' button. Required input files for the workflow to completely execute are:
-	your MS data in .mzML format for feature detection and quantitation in the ‚Signal processing‘ section of the workflow. Just double click the Input Files node and add your files.
-	adduct files and LIPID MAPS database files for the identification using accurate mass. Add the corresponding files in the ‚Structure mapping files‘ section of the workflow.
-	your MS data in .mzML format including MS2 information. Set this file in the Input File node in the ‚Identification using spectral matching‘ section.
- an MS2 spectrum database in .mzML format. We provide a zipped version of the MassBank database (due to size restrictions this file is not part of the OpenMS installation in KNIME). The unzipped file has to be situated in the ‚Chemistry‘ subfolder of the OpenMS plugin of your KNIME installation, for example in ‚…\KNIME\plugins\de.openms.win32.x86_64_2.0.0.201602221444\payload\share\OpenMS\CHEMISTRY\’.
-	a training dataset for lipid retention time prediction containing retention times and SMILES structure notations of identified training compounds. We provide an example file (example_train_dataset.csv) for the execution of the workflow. Training data should of course be adapted for your specific chromatographic setup. The training data has to be set in the CSV Reader node labeled ‚Train dataset’ in the ‚AMS candidate filtering by retention time’ section. Note that more than 40 training compounds are required for this specific model.

The ‚AMS candidate filtering by retention time’ section for training and application of a lipid retention time prediction model is based on the model described in doi:10.1021/acs.analchem.5b01139. It requires several additional input and database files. For visual clarity, the respective input nodes are located in KNIME meta nodes (grey colored nodes). Meta nodes aggregate several connected KNIME nodes into one node. You can open a meta node by double clicking on it.
-	a normalization model for the physicochemical descriptors used in the prediction. We supply a normalization model based on the whole LIPID MAPS database (LM_norm_model.zip). The respective Model Reader node is located in the ‚Compute physicochemical descriptors‘ meta node. This node accepts zip files.
-	(normalized) physicochemical descriptors for all LIPID MAPS compounds (LM_SVR_in.csv). The respective CSV Reader node is labeled ‚LM SVR features‘ and located in the ‚Predict LIPID MAPS database RTs‘ meta node.
-	the LIPID MAPS database. We provide a zipped version (LMSDFDownload18Mar14FinalAll.zip), the unzipped .SDF file is required as input for the SDF Reader node in the ‚Predict LIPID MAPS database RTs‘ meta node.

##Customization
Due to the size of this workflow, we will point out various parts where customization/adaption of parameters is possible, sensible or required:
-	First of all would be of course the OpenMS nodes, i.e., the FeatureFinderMetabo, MapAlignerPoseClustering, FeatureLinkerUnlabeledQT, AccurateMassSearch and MetaboliteSpectralMatcher node. For brevity, we have to refer to the KNIME node description available when selecing a node and the OpenMS web documentations therein.
-	The R Snippet node in the ‚Statistical Analysis‘ section which performs a t-test for two groups. Configuration of R nodes allows to change the internal scripts. The first two lines of this script specify which files belong to which group. This will probably have to be adapted to your study design.
-	Similarly, the R Snippet node labeled ‚FDR‘ in the same section contains a small script with  a fixed (0.01) false discovery rate you could adapt.
-	The R Snippet node in the ‚Interactive Visualization‘ section also contains a small script which internally sets Fold change and p-value thresholds.
-	The R Snippet node in the ‚Identification using accurate mass search‘ section contains a script which matches MS1 features to MS/MS identifications if m/z and retention time agree. Tolerances for retention time and m/z errors are defined in the first two lines of the script.
