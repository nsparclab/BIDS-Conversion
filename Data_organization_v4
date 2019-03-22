import os
import glob
import subprocess
import shutil
import json
import nibabel
import numpy as np
import pandas as pd


#Get input from user about PID, Session info
PID = input('Enter participant ID w/ TP info (in this format NABM999-1):') #Ex. NABM165-1
SESSION = input ('Enter only the session number(for example, "1a"):') #Ex. 1a
FULL_PID = PID + SESSION[-1] #Ex. NABM165-1a

# Create/get basic paths using PID input info
source_path = '/m/RawData/OrganizedExternalData/Dicoms/NABM/' + PID #/.../NABM165-1
raw_sub_path = os.path.join(source_path,FULL_PID) #/.../NABM165-1/NABM165-1a

#Work in progress/ BIDS path
wip_BIDS_path = '/m/InProcess/3T/NABM/fMRI/BIDS'

# File parts for sub and ses. Equivalent to inputted values- can delete later
sub = raw_sub_path.split('/')[6].split('-')[0]  # Extracts NABM165; does BIDS need sub as just 165 or NABM in ok?
ses = raw_sub_path.split('/')[-1].split('-')[-1]  # Extracts 1a. Equivalent to SESSION
sub_numberOnly = raw_sub_path.split('/')[-1].split('-')[0][-3:]

# Setup to make sub folder names in wip_path later via mkdir
# Where BIDS/ NIfti will be sent (wip folders)
final_sub_path_subNumber = os.path.join(wip_BIDS_path, 'sub-' + sub_numberOnly)
final_sub_path_withSession = os.path.join(final_sub_path_subNumber, 'ses-' + ses)

# Prepare + mkdir in BIDS organization
# Paths for anat, func, dwi
final_anat_path = os.path.join(final_sub_path_withSession, 'anat')
final_func_path = os.path.join(final_sub_path_withSession, 'func')
final_dwi_path = os.path.join(final_sub_path_withSession, 'dwi')
final_fmap_path = os.path.join(final_sub_path_withSession, 'fmap')

#logfile directories on B drive
CUE_logfile_path = ('/m/InProcess/3T/NABM/fMRI/Logfiles/CUE_Logfiles')
AAT_logfile_path = ('/m/InProcess/3T/NABM/fMRI/Logfiles/AAT_Logfiles')

# Keywords for folders of dcm we want to convert
# Selecting SPECIFIC folders to convert so we don't convert the whole dcm dir/ whole scan

# tfl_b1map (2 of them). These are the fmaps before anat sequences
#raw_b1map_source = glob.glob(os.path.join(raw_sub_path, '*b1map*'))
#raw_b1map_path1 = raw_b1map_source[0]
#raw_b1map_path2 = raw_b1map_source[1]

# FieldMap (2 of them). These are the fmaps before task-based sequences
raw_FieldMap_source = glob.glob(os.path.join(raw_sub_path, '*_FieldMap*'))
raw_FieldMap_path1 = raw_FieldMap_source[0]
raw_FieldMap_path2 = raw_FieldMap_source[1]

# SpinEchoFieldMap (2 of them). These are the fmaps between AAT and CUE task-based sequences
raw_SpinEchoFmap_PA_source = glob.glob(os.path.join(raw_sub_path, '*_SpinEchoFieldMap_PA*'))
raw_SpinEchoFmap_AP_source = glob.glob(os.path.join(raw_sub_path, '*_SpinEchoFieldMap_AP*'))
raw_SpinEchoFmap_PA_path = raw_SpinEchoFmap_PA_source[0]
raw_SpinEchoFmap_AP_path = raw_SpinEchoFmap_AP_source[0]

# T1w- get raw dicom path
raw_T1w_source = glob.glob(os.path.join(raw_sub_path,'*T1w*'))
raw_T1w_path = 'T1w'
raw_T1w_Resliced_path = 'T1w Resliced' #we do not use this in analysis but need to account for it
if raw_T1w_source[0].__contains__('Thick Range'):
    raw_T1w_Resliced_path = raw_T1w_source[0]
else:
    raw_T1w_path = raw_T1w_source[0]

if raw_T1w_source[1].__contains__('Thick Range'):
    raw_T1w_Resliced_path = raw_T1w_source[1]
else:
    raw_T1w_path = raw_T1w_source[1]

# T2w- get raw dicom path
raw_T2w_path = glob.glob(os.path.join(raw_sub_path, '*T2w*'))[-1] #T2

# REST 1- get raw dicom paths
raw_tfMRI_REST1_source = glob.glob(os.path.join(raw_sub_path, '*REST1*')) # path with both REST1 keys
raw_tfMRI_REST1_path = 'rest1' # changed later
raw_tfMRI_REST1_SBRef_path = glob.glob(os.path.join(raw_sub_path, '*REST1_PA_2mm_SBRef*')) #Rest 1 SBRef
if raw_tfMRI_REST1_source[0].__contains__('SBRef'):
    raw_tfMRI_REST1_SBRef_path = raw_tfMRI_REST1_source[0]
else:
    raw_tfMRI_REST1_path = raw_tfMRI_REST1_source[0]

if raw_tfMRI_REST1_source[1].__contains__('SBRef'):
    raw_tfMRI_REST1_SBRef_path = raw_tfMRI_REST1_source[1]
else:
    raw_tfMRI_REST1_path = raw_tfMRI_REST1_source[1]

# REST 2- get raw dicom paths
raw_tfMRI_REST2_source = glob.glob(os.path.join(raw_sub_path, '*REST2*')) # path with both REST2 keys
raw_tfMRI_REST2_path = 'rest2' # changed later
raw_tfMRI_REST2_SBRef_path = 'rest2 sbref'
if raw_tfMRI_REST2_source[0].__contains__('SBRef'):
    raw_tfMRI_REST2_SBRef_path = raw_tfMRI_REST2_source[0]
else:
    raw_tfMRI_REST2_path = raw_tfMRI_REST2_source[0]

if raw_tfMRI_REST2_source[1].__contains__('SBRef'):
    raw_tfMRI_REST2_SBRef_path = raw_tfMRI_REST2_source[1]
else:
    raw_tfMRI_REST2_path = raw_tfMRI_REST2_source[1]

#AAT A- get raw dicom paths
raw_tfMRI_AAT_A_source = glob.glob(os.path.join(raw_sub_path, '*AAT_A*')) # path with both AAT_A keys
raw_tfMRI_AAT_A_path = 'AAT_A'
raw_tfMRI_AAT_A_SBRef_path = 'AAT_A sbref'
if raw_tfMRI_AAT_A_source[0].__contains__('SBRef'):
    raw_tfMRI_AAT_A_SBRef_path = raw_tfMRI_AAT_A_source[0]
else:
    raw_tfMRI_AAT_A_path = raw_tfMRI_AAT_A_source[0]

if raw_tfMRI_AAT_A_source[1].__contains__('SBRef'):
    raw_tfMRI_AAT_A_SBRef_path = raw_tfMRI_AAT_A_source[1]
else:
    raw_tfMRI_AAT_A_path = raw_tfMRI_AAT_A_source[1]

#AAT B- get raw dicom paths
raw_tfMRI_AAT_B_source = glob.glob(os.path.join(raw_sub_path, '*AAT_B*'))
raw_tfMRI_AAT_B_path = 'AAT_B'
raw_tfMRI_AAT_B_SBRef_path = 'AAT_B sbref'
if raw_tfMRI_AAT_B_source[0].__contains__('SBRef'):
    raw_tfMRI_AAT_B_SBRef_path = raw_tfMRI_AAT_B_source[0]
else:
    raw_tfMRI_AAT_B_path = raw_tfMRI_AAT_B_source[0]

if raw_tfMRI_AAT_B_source[1].__contains__('SBRef'):
    raw_tfMRI_AAT_B_SBRef_path = raw_tfMRI_AAT_B_source[1]
else:
    raw_tfMRI_AAT_B_path = raw_tfMRI_AAT_B_source[1]

#CUE A- get raw dicom paths
#Need to include a check for number of dcm inside
# len(fnmatch.filter(os.listdir(raw_tfMRI_CUE_A_source[1]), '*.dcm'))--- this will cound dcm/ima files inside
raw_tfMRI_CUE_A_source = glob.glob(os.path.join(raw_sub_path, '*CUE_A*'))
raw_tfMRI_CUE_A_path = 'CUE_A'
raw_tfMRI_CUE_A_SBref_path = 'CUE_A sbref'
if raw_tfMRI_CUE_A_source[0].__contains__('SBRef'):
    raw_tfMRI_CUE_A_SBRef_path = raw_tfMRI_CUE_A_source[0]
else:
    raw_tfMRI_CUE_A_path = raw_tfMRI_CUE_A_source[0]

if raw_tfMRI_CUE_A_source[1].__contains__('SBRef'):
    raw_tfMRI_CUE_A_SBRef_path = raw_tfMRI_CUE_A_source[1]
else:
    raw_tfMRI_CUE_A_path = raw_tfMRI_CUE_A_source[1]

#CUE B- get raw dicom paths
raw_tfMRI_CUE_B_source = glob.glob(os.path.join(raw_sub_path, '*CUE_B*'))
raw_tfMRI_CUE_B_path = 'CUE_B'
raw_tfMRI_CUE_B_SBRef_path = 'CUE_B sbref'
if raw_tfMRI_CUE_B_source[0].__contains__('SBRef'):
    raw_tfMRI_CUE_B_SBRef_path = raw_tfMRI_CUE_B_source[0]
else:
    raw_tfMRI_CUE_B_path = raw_tfMRI_CUE_B_source[0]

if raw_tfMRI_CUE_B_source[1].__contains__('SBRef'):
    raw_tfMRI_CUE_B_SBRef_path = raw_tfMRI_CUE_B_source[1]
else:
    raw_tfMRI_CUE_B_path = raw_tfMRI_CUE_B_source[1]

# Make dir for Participant: subj name, ses name. Then break into further BIDS required parts.
if not os.path.exists(final_sub_path_subNumber): #check to see if sub folder already created from previous session
    os.mkdir(final_sub_path_subNumber)
os.mkdir(final_sub_path_withSession) #/sub-NNN/ses-Na

# Make dir for 'anat', 'func', 'dwi', 'fmap' in wip_BIDS_path
os.mkdir(final_anat_path)
os.mkdir(final_func_path)
os.mkdir(final_dwi_path)
os.mkdir(final_fmap_path)

# Convert each .nii.gz INDIVIDUALLY using dcm2niix, then immediately rename (to deal with error from v3)
# SBRef files have added %z flag which adds 'epfid2d1_112' in order to distinguish between the BOLD- otherwise they were converting with same name

# Change to final_func_path to convert and rename .NII.GZ IMAGE FILES
os.chdir(final_func_path)

#REST1
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_tfMRI_REST1_path]) #REST1
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_REST1_PA_2mm_.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-rest_run-01_bold.nii.gz')

#REST1 SBREF
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i_%z', '-b','y','-z','y','-g','y',raw_tfMRI_REST1_SBRef_path]) #REST1 SBRef
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_REST1_PA_2mm__epfid2d1_112.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-rest_run-01_sbref.nii.gz')

#REST2
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_tfMRI_REST2_path]) #REST2
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_REST2_PA_2mm_.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-rest_run-02_bold.nii.gz')

#REST2 SBREF
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i_%z', '-b','y','-z','y','-g','y',raw_tfMRI_REST2_SBRef_path]) #REST2 SBRef
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_REST2_PA_2mm__epfid2d1_112.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-rest_run-02_sbref.nii.gz')

#AAT_A
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_tfMRI_AAT_A_path]) #AAT_A
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_AAT_A_PA_2mm_.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-01_bold.nii.gz')
        BIDS_named_AAT_1 = glob.glob(os.path.join(final_func_path, "*_task-aat_run-01_bold.nii.gz"))[0] #get newly BIDS named .nii path-- used for fmap json "IntendedFor"

#AAT_A SBREF
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i_%z', '-b','y','-z','y','-g','y',raw_tfMRI_AAT_A_SBRef_path]) #AAT_A SBRef
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_AAT_A_PA_2mm__epfid2d1_112.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-01_sbref.nii.gz')

#AAT_B
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_tfMRI_AAT_B_path]) #AAT_B
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_AAT_B_PA_2mm_.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-02_bold.nii.gz')
        BIDS_named_AAT_2 = glob.glob(os.path.join(final_func_path, "*_task-aat_run-02_bold.nii.gz"))[0]

#AAT_B SBREF
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i_%z', '-b','y','-z','y','-g','y',raw_tfMRI_AAT_B_SBRef_path]) #AAT_B SBRef
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_AAT_B_PA_2mm__epfid2d1_112.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-02_sbref.nii.gz')

#CUE_A
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_tfMRI_CUE_A_path]) #CUE_A
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_CUE_A_PA_2mm_.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-01_bold.nii.gz')
        BIDS_named_CUE_1 = glob.glob(os.path.join(final_func_path, "*_task-cue_run-01_bold.nii.gz"))[0]

#CUE_A SBREF
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i_%z', '-b','y','-z','y','-g','y',raw_tfMRI_CUE_A_SBRef_path]) #CUE_A SBRef
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_CUE_A_PA_2mm__epfid2d1_112.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-01_sbref.nii.gz')

#CUE_B
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_tfMRI_CUE_B_path]) #CUE_B
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_CUE_B_2mm_.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-02_bold.nii.gz')
        BIDS_named_CUE_2 = glob.glob(os.path.join(final_func_path, "*_task-cue_run-02_bold.nii.gz"))[0]

#CUE_B SBREF
subprocess.call(['dcm2niix', '-o', final_func_path, '-f', '%p_%n%i_%z', '-b','y','-z','y','-g','y',raw_tfMRI_CUE_B_SBRef_path]) #CUE_B SBRef
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_CUE_B_2mm__epfid2d1_112.nii.gz'):
        os.rename(filename, 'sub-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-02_sbref.nii.gz')

# Change to final_anat_path folder to convert and rename anat images
os.chdir(final_anat_path)

#T1
subprocess.call(['dcm2niix', '-o', final_anat_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_T1w_path]) #T1
for filename in os.listdir(final_anat_path):
    if filename.__contains__('T1w_MPR_.nii.gz'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_T1w' + '.nii.gz')

#T2
subprocess.call(['dcm2niix', '-o', final_anat_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_T2w_path]) #T2
for filename in os.listdir(final_anat_path):
    if filename.__contains__('T2w_SPC_.nii.gz'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_T2w' + '.nii.gz')

# Change to final_fmap_path folder to convert and rename fmaps
os.chdir(final_fmap_path)

#FieldMap1-- outputs 2 magnitudes (e1, e2)
subprocess.call(['dcm2niix', '-o', final_fmap_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_FieldMap_path1])
for filename in os.listdir(final_fmap_path):
    if filename.__contains__('_e1.nii.gz'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_magnitude1' + '.nii.gz')
    elif filename.__contains__('FieldMap__e2.nii.gz'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_magnitude2' + '.nii.gz')

#FieldMap2-- outputs 1 phase image (_e2_ph)
subprocess.call(['dcm2niix', '-o', final_fmap_path, '-f', '%p_%n%i', '-b','y','-z','y','-g','y',raw_FieldMap_path2])
for filename in os.listdir(final_fmap_path):
    if filename.__contains__('FieldMap__e2_ph.nii.gz'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_phasediff' + '.nii.gz')

# Rename FMAP. json files from dcm2niix conversion, change to final_fmap_path
for filename in os.listdir(final_fmap_path):
    if filename.__contains__('FieldMap__e1.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_magnitude1' + '.json')
    elif filename.__contains__('FieldMap__e2.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_magnitude2' + '.json')
    elif filename.__contains__('FieldMap__e2_ph.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_phasediff' + '.json')

# # Edit fmap .jsons to include "IntendedFor" and both TE in phasediff
fmap_json_source = glob.glob(os.path.join(final_fmap_path,"*json")) #only .json files from final_func_path

for fmap_json in fmap_json_source:
    if fmap_json.__contains__("phasediff.json"):
        with open(fmap_json) as fm:
            json_loaded = json.load(fm)
            #json_loaded['TEEEESTT KEY'] = "test value"
            del json_loaded['EchoNumber']
            del json_loaded['EchoTime']
            json_loaded['EchoTime1'] = "0.00492"
            json_loaded['EchoTime2'] = "0.00738"
            json_loaded['IntendedFor'] = [BIDS_named_AAT_1, BIDS_named_AAT_2, BIDS_named_CUE_1, BIDS_named_CUE_2]
            print(fm)
        with open(fmap_json, 'w') as fm_w:
            json_loaded = json.dump(json_loaded, fm_w)

# Rename FUNC .json files from dcm2niix conversion, change to final_func_path
os.chdir(final_func_path)

#REST1
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_REST1_PA_2mm_.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-rest_run-01_bold' + '.json')

#REST1 SBREF
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_REST1_PA_2mm__epfid2d1_112.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-rest_run-01_sbref' + '.json')

#REST2
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_REST2_PA_2mm_.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-rest_run-02_bold' + '.json')

#REST2 SBREF
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_REST2_PA_2mm__epfid2d1_112.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-rest_run-02_sbref' + '.json')

#AAT_A
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_AAT_A_PA_2mm_.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-01_bold' + '.json')

#AAT_A SBREF
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_AAT_A_PA_2mm__epfid2d1_112.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-01_sbref' + '.json')

#AAT_B
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_AAT_B_PA_2mm_.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-02_bold' + '.json')

#AAT_B SBREF
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_AAT_B_PA_2mm__epfid2d1_112.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-02_sbref' + '.json')

#CUE_A
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_CUE_A_PA_2mm_.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-01_bold' + '.json')

#CUE_A SBREF
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_CUE_A_PA_2mm__epfid2d1_112.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-01_sbref' + '.json')

#CUE_B
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_CUE_B_2mm_.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-02_bold' + '.json')

#CUE_B SBREF
for filename in os.listdir(final_func_path):
    if filename.__contains__('tfMRI_CUE_B_2mm__epfid2d1_112.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-02_sbref' + '.json')

# Rename ANAT .json files from dcm2niix conversion, change to final_anat_path
os.chdir(final_anat_path)
#T1
for filename in os.listdir(final_anat_path):
    if filename.__contains__('T1w_MPR_.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_T1w' + '.json')
#T2
for filename in os.listdir(final_anat_path):
    if filename.__contains__('T2w_SPC_.json'):
        os.rename(filename, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_T2w' + '.json')


# Conversion check-- correct dimensions per BOLD (rest + task) sequence. Compare collected to ideal/template later
AAT_correct_dim = [4, 112, 112, 70, 314, 0, 0, 0] # what the 'dim' should be in AAT niftis
AAT_correct_dim_np = np.array(AAT_correct_dim)
CUE_correct_dim = [4, 112, 112, 70, 370, 0, 0, 0]
CUE_correct_dim_np = np.array(CUE_correct_dim) # what the 'dim' should be in CUE niftis
REST_correct_dim = [4, 112, 112, 70, 300, 0, 0, 0]
REST_correct_dim_np = np.array(REST_correct_dim)

# Error logging from dcm2niix-- saving to .txt rather than terminal
error_log_path = '/m/InProcess/3T/NABM/fMRI/dcm2niix_errors' #this is hard coded so make sure this folder exists
error_log_name = os.path.join(error_log_path, PID+"_conversionerrors.txt")
error_log = open(error_log_name, 'w')

#Note: for each of these I needed to convert the header['dim] to str to be able to use .write() in error log

# Check AAT_A conversion
AAT_A_nifti = glob.glob(os.path.join(final_func_path, '*aat_run-01_bold.nii.gz*'))
AAT_A_img = nibabel.load(AAT_A_nifti[0]) #has to be [0] so it's a string not list
AAT_A_header = AAT_A_img.header
str_AAT_A_dim = str(AAT_A_header['dim'])
if np.array_equal([AAT_correct_dim_np], [AAT_A_header['dim']]):
    print('AAT_A nifti was converted successfully')
    error_log.write('AAT_A nifti was converted successfully, the dim is:\n')
    error_log.write(str_AAT_A_dim)
else:
    print('AAT_A dimension error- did not convert')
    error_log.write('AAT_A dimension error- did not convert, the dim is:\n')
    error_log.write(str_AAT_A_dim)

# Check AAT_B conversion
AAT_B_nifti = glob.glob(os.path.join(final_func_path, '*aat_run-02_bold.nii.gz*'))
AAT_B_img = nibabel.load(AAT_B_nifti[0]) #has to be [0] so it's a string not list
AAT_B_header = AAT_B_img.header
str_AAT_B_dim = str(AAT_B_header['dim'])
if np.array_equal([AAT_correct_dim_np], [AAT_B_header['dim']]):
    print('AAT_B nifti was converted successfully')
    error_log.write('AAT_B nifti was converted successfully, the dim is:\n')
    error_log.write(str_AAT_B_dim)
else:
    print("AAT_B dimension error- did not convert")
    error_log.write('AAT_B dimension error- did not convert, the dim is: \n')
    error_log.write(str_AAT_B_dim)

# Check CUE_A conversion
CUE_A_nifti = glob.glob(os.path.join(final_func_path, '*cue_run-01_bold.nii.gz'))
CUE_A_img = nibabel.load(CUE_A_nifti[0])
CUE_A_header = CUE_A_img.header
str_CUE_A_dim = str(CUE_A_header['dim'])
if np.array_equal([CUE_correct_dim_np], [CUE_A_header['dim']]):
    print('CUE_A nifti was converted successfully')
    error_log.write('CUE_A nifti was converted successfully, the dim is:\n')
    error_log.write(str_CUE_A_dim)
else:
    print('CUE_A dimension error- did not convert')
    error_log.write('CUE_A dimension error- did not convert, the dim is:\n')
    error_log.write(str_CUE_A_dim)

# Check CUE_B conversion
CUE_B_nifti = glob.glob(os.path.join(final_func_path, '*cue_run-02_bold.nii.gz*'))
CUE_B_img = nibabel.load(CUE_B_nifti[0])
CUE_B_header = CUE_B_img.header
str_CUE_B_dim = str(CUE_B_header['dim'])
if np.array_equal([CUE_correct_dim_np], [CUE_B_header['dim']]):
    print('CUE_B nifti was converted successfully')
    error_log.write('CUE_B nifti was converted successfully, the dim is:\n')
    error_log.write(str_CUE_B_dim)
else:
    print('CUE_B dimension error- did not convert')
    error_log.write('CUE_B dimension error- did not convert, the dim is:\n')
    error_log.write(str_CUE_B_dim)

# Check Rest 1 conversion
REST_1_nifti = glob.glob(os.path.join(final_func_path, '*rest_run-01_bold.nii.gz*'))
REST_1_img = nibabel.load(REST_1_nifti[0])
REST_1_header = REST_1_img.header
str_REST_1_dim = str(REST_1_header['dim'])
if np.array_equal([REST_correct_dim], [REST_1_header['dim']]):
    print('REST1 nifti was converted successfully')
    error_log.write('REST1 nifti was converted successfully, the dim is:\n')
    str_REST_1_dim = str(REST_1_header['dim'])
else:
    print('REST1 dimension error- did not convert')
    error_log.write('REST1 dimension error- did not convert, the dim is:\n')
    str_REST_1_dim = str(REST_1_header['dim'])

# Check Rest 2 conversion
REST_2_nifti = glob.glob(os.path.join(final_func_path, '*rest_run-02_bold.nii.gz*'))
REST_2_img = nibabel.load(REST_1_nifti[0])
REST_2_header = REST_1_img.header
str_REST_2_dim = str(REST_2_header['dim'])
if np.array_equal([REST_correct_dim], [REST_2_header['dim']]):
    print('REST2 nifti was converted successfully')
    error_log.write('REST2 nifti was converted successfully, the dim is:\n')
    error_log.write(str_REST_2_dim)
else:
    print('REST2 dimension error- did not convert')
    error_log.write('REST2 dimension error- did not convert, the dim is:\n')
    error_log.write(str_REST_2_dim)

#Close error log
error_log.close()


# Open, read, rewrite, dump JSON files to include task info in func scans
json_source = glob.glob(os.path.join(final_func_path,"*json")) #only .json files from final_func_path

for json_filename in json_source:
    if json_filename.__contains__("aat"):
        try:
            with open (json_filename) as f:
                data = json.load(f)
                data ['TaskName'] = 'AAT'
            with open(json_filename, 'w') as w:
                json.dump(data, w, indent=4)
        except:
            print(json_filename, " - Doesn't seem to be a valid json. Check!!!")
    elif json_filename.__contains__("cue"):
        try:
            with open (json_filename) as f:
                data = json.load(f)
                data ['TaskName'] = 'CUE'
            with open(json_filename, 'w') as w:
                json.dump(data, w, indent=4)
        except:
            print(json_filename, " - Doesn't seem to be a valid json. Check!!!")
    elif json_filename.__contains__("rest"):
        try:
            with open (json_filename) as f:
                data = json.load(f)
                data ['TaskName'] = 'Rest'
            with open (json_filename, 'w') as w:
                json.dump(data, w, indent=4)
        except:
            print(json_filename, " - Doesn't seem to be a valid json. Check!!!")


# Copy eventstsv logfiles (AAT + CUE) to BIDS func folder using shutil
AAT_logfile_path = '/m/InProcess/3T/NABM/fMRI/Logfiles/AAT_Logfiles'
CUE_logfile_path = '/m/InProcess/3T/NABM/fMRI/Logfiles/CUE_Logfiles'
source_AAT_txts = glob.glob(os.path.join(AAT_logfile_path, "*txt*")) # need to use os.listdir to get the files in logfile_source
source_CUE_txts = glob.glob(os.path.join(CUE_logfile_path, "*txt*"))
destination = final_func_path

for files in source_CUE_txts:
    if files.endswith("BIDS_EventsFile.txt") & files.__contains__(PID):
        shutil.copy(files,destination)

for files in source_AAT_txts:
    if files.endswith("BIDS_EventsFile.txt") & files.__contains__(PID):
        shutil.copy(files,destination)



# Rename the logfiles into BIDS format
os.chdir(final_func_path)
for fname in os.listdir('.'):
    if fname.endswith("Cue_A_BIDS_EventsFile.txt"):
        os.rename(fname, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-01_events' + '.tsv')
    elif fname.endswith("Cue_B_BIDS_EventsFile.txt"):
        os.rename(fname, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-cue_run-02_events' + '.tsv')
    elif fname.endswith("AAT_Run_A_BIDS_EventsFile.txt"):
        os.rename(fname, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-01_events' + '.tsv')
    elif fname.endswith("AAT_Run_B_BIDS_EventsFile.txt"):
        os.rename(fname, 'sub' + '-' + sub_numberOnly + '_ses-' + ses + '_task-aat_run-02_events' + '.tsv')

# Edit events.tsv CUE files. (Had an issue with rows not equal to columns, and stimuli name)
# Need to add '.' to NaN columns, and change stim name when VAS occurs to 'VAS_Presented'
# Will apply this code to all files but not needed for scans after this problem was identified (feb 2019)

# Get tsv CUE Files
tsv_CUE1 = glob.glob(os.path.join(final_func_path, '*cue_run-01_events*'))[0]
tsv_CUE2 = glob.glob(os.path.join(final_func_path, '*cue_run-02_events*'))[0]

# Read tsv files, edit, then copy to original. First missing data in last column, then VAS_Presented.jpg issue
# CUE 1--deal with missing info in last column
read_CUE1 = pd.read_csv(tsv_CUE1, delimiter='\t') # Use pandas to read CUE1 tsv file
new_CUE1 = read_CUE1.fillna('.') # Replace NaN values with '.' and save as new file
new_CUE1['stim_file'] = new_CUE1['stim_file'].replace(['.'],'VAS_Presented.jpg') # Edit 'stim_file' column only
new_CUE1.to_csv(tsv_CUE1, index=False, sep='\t') #index=False--> do not print row index #s from dataframe. # Print new_CUE1 to / overwrite tsv file

# CUE2--deal with missing info in last column
read_CUE2 = pd.read_csv(tsv_CUE2, delimiter='\t') # Use pandas to read CUE1 tsv file
new_CUE2 = read_CUE2.fillna('.') # Replace NaN values with '.' and save as new file
new_CUE2['stim_file'] = new_CUE2['stim_file'].replace(['.'],'VAS_Presented.jpg') # Edit 'stim_file' column only
new_CUE2.to_csv(tsv_CUE2, index=False, sep='\t') #index=False--> do not print row index #s from dataframe. # Print new_CUE1 to / overwrite tsv file
