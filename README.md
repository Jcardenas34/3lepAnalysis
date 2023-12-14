# Charged Higgs to Hplus feasibility study

## Background and Resources

A feasibility study to motivate H+ $`\rightarrow`$ SUSY with 3 leptons in the final state (electrons and muons) analysis. Charged Higgs to SUSY has the ability to probe the unexplored wedge region of $`tan\beta$\ v\ M_A`$ parameter space where H+ to SM processes are supressed. The 3 lepton final state experiences an excess. 
[3lep Summary Slides](https://gitlab.cern.ch/arewoldt/3lep-analysis/-/blob/874fc3e295f04906f7c7d9e1d52746f5311d19ab/HplusSUSY3lepSummary.pdf)

Juan's UTA-ATLAS getting started gitlab: [Quick Start Documentation](https://jcardenas34.github.io/UTA-HBSM-New-Member-Quick-Start-Documentation/)

Some relevant papers: 

- [PdgId Reference](https://pdg.lbl.gov/2006/reviews/montecarlorpp.pdf)
- [Heavy Higgs as a Portal to the Supersymmetric Electroweak Sector](https://arxiv.org/pdf/1811.11918.pdf)
- [Chargino–Neutralino Production - Recursive Jigsaw Reconstruction - 2 or 3 Lepton Final State](https://arxiv.org/pdf/1806.02293.pdf)
- [Search for Chargino-Neutralino Production - 3 Lepton Final State](https://arxiv.org/pdf/2106.01676.pdf)
- [MSSM Benchmark Scenarios - 2019](https://arxiv.org/pdf/1808.07542.pdf)

## Event Generation and Versioning
3 lep analysis repository only contains documents that I felt were relevant and worth sharing. Tier3 contains all files (DAOD, runcards, scripts, etc)
    path: /cluster/home/amyrewoldt

### AthDerivation version used to run over PythonScripts

    setupATLAS
    cd ProjectDirectory
    asetup AthDerivation,21.2.48.0, here

*csvFilesMVA primarily made by running python script*

    /cluster/home/amyrewoldt/21.2.48/run/MVA_3lep_v3.py


### Standalone Event Generation Using Madgraph 2.7.3

	wget https://launchpad.net/mg5amcnlo/2.0/2.7.x/+download/MG5_aMC_v2.7.3.tar.gz
	gunzip MG5_aMC_v2.7.3.tar.gz
	cd MG5_aMC_v2_7_3
	wget https://gitlab.cern.ch/arewoldt/3lep-analysis/-/blob/master/Standalone-MadgraphEventGeneration/proc_card_mg5_3lep.dat
	./bin proc_card_mg5_3lep.dat
There should be a directory *mssm_3lep* after completion of this step. This directory should be set up to run mssm 3lep process. 
To generate events:

	./bin/generate_events
For DAOD's run (make sure to change the setup script to either setupATLAS or path to your own setup script)

	./Generate.sh

### Set up the madgraph output for use in the evgen step

Make directory for evgen and derivation files and copy madgraph output
	
	mkdir dirname
	cd dirname
	cp /path/to/mg/run/unweighted_events.lhe.gz .
	gunzip unweighted_events.lhe.gz
	mv unweighted_events.lhe madgraph.000001.Example._00001.events 
	tar cvzf madgraph.000001.Example._00001.events.tar.gz madgraph.000001.Example._00001.events 

### Run evgen

	mkdir evgen
	cd evgen
	asetup 19.2.5.36,AtlasProduction,here

Once your release is set up, you can run the evgen step with the following:

	Generate_tf.py --ecmEnergy=13000. --maxEvents=-1 --runNumber=000001 --firstEvent=1 --randomSeed=123456 --outputEVNTFile=evgen.root --jobConfig=/cluster/home/bburghgr/cards/testlhe.py --inputGeneratorFile=../madgraph.000001.Example._00001.events.tar.gz

After running the `Generate_tf.py` command, you should have a file named *evgen.root*

### Run derivation

From your *dirname* directory make new directory for DAOD's (truth or reconstruction etc).	

	mkdir truth
	cd truth
	asetup 21.2.48.0,AthDerivation,here
For truth

	Reco_tf.py --inputEVNTFile ../evgen/evgen.root --outputDAODFile test.pool.root --reductionConf TRUTH1

For reco refer to the *mk_hits_script.sh* and *merge_and_mkaod.sh* scripts directory and modify the commands to fit your needs. 
	
### OTFR Event Generation

In otfr_test directory

	asetup AtlasProduction,19.2.5.32,here
To run:

	cd test
	Generate_tf.py --ecmEnergy=13000. --maxEvents=10000 --runNumber=346281 --firstEvent=1 --randomSeed=123456 --outputEVNTFile=test.root --jobConfig=../share_DSID346xxx_MC15.346281.aMcAtNloPy8EvtGen_A14NNPDF30NLO_HpH_H1000_3lep_filter_D1.py

To change the mass of H+ modify --jobConfig argument tag (i.e. share_DSID346xxx_MC15.346281.aMcAtNloPy8EvtGen_A14NNPDF30NLO_HpH_H\*\_3lep_filter_D1.py replacing * with mass of H+). If the file doesn't exist already cp existing file and modify the name.

To change the mass of electroweakinos or other SUSY particles edit the common_MadGraph\_... card which will write the masses of SUSY particles to a param_card and use madgraph to handle their decay.

This should generate a test.root file and you can do what you will with that. It's been a while so hopefully this generates what it's supposed to generate.

## JupyterNotebooks on tier3
### Setup Instructions
[HERE](https://gitlab.cern.ch/arewoldt/3lep-analysis/-/blob/master/JupyterNotebooks/Jupyter-notebook-tier3-setup.pdf)
### After setup
Locally run:

    ssh -N -f -L localhost:8000:localhost:8000 usrname@master.tier3-atlas.uta.edu
On tier3

    source miniconda/bin/activate
    jupyter notebook --port=8000 --no-browser
    
Copy http provided and paste into browser of choice (http should look like: *http://127.0.0.1:8000/?token=d468f0e00aa7c8a9abcc15f4177a985d818d4aa639fbaf16*).

ur welcome
