# HistFitter to Pyhf Workspace Conversion

Procedure (Setup):

    ssh USERNAME@lxplus.cern.ch
    cd eos/home-*/USERNAME
    mkdir ANALYSIS
    cd ANALYSIS

Procedure (Make MonoJetAnalysis):

    setupATLAS
    lsetup git
    git clone --recursive https://:@gitlab.cern.ch:8443/atlas-phys/exot/jdm/ANA-EXOT-2018-06/monojet.git MonoJetAnalysis
    cd MonoJetAnalysis/source
    setupATLAS
    asetup AnalysisBase,21.2.75,here 
    cd ..
    # The following steps may not be neccessary
    mkdir build
    cd build
    cmake ../source
    make -j5
    source x86_64-centos7-gcc62-opt/setup.sh

Procedure (Extract xml from Histfitter workspace):

    cd source/MonoJetAnalysis/share
    git clone https://gitlab.cern.ch/atlas-phys/exot/jdm/ANA-EXOT-2018-06/Interpretation.git
    cd Interpretation/
    source setup.sh
    cd HistFitter
    nohup HistFitter.py -t -w -F bkg -m ALL -f -D "corrMatrix" -u "--strategy 1111 --suffix _3kfactors_XXXX --years 15,16,17,18" -x ../WSMaker_splitTop.py > log_3kfactors_XXXX.log &
    tail -f log_3kfactors_XXXX.log # See logs to make sure everything is running
    # It may not be neccessary for the whole analysis to run, just for the config to finish
    mv ShapeFit_std_CRFit_3kfactors_XXXX /eos/home-*/USERNAME/ShapeFit_std_CRFit_3kfactors_XXXX
    exit

The rest of the work is done in the python notebook.

Errors and Solutions: 
  - HistFitter.py -t -w -f -m ALL -u "--doUseSR --strategy 1 --suffix 0001" -F bkg -x ../WSMaker.py
    IOError: [Errno 2] No such file or directory: '../../monojet.conf'
    Solution: monojet.conf is present in MonoJetAnalysis source, make sure you have it installed in the right spot.

