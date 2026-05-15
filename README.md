# Predictive Power Management in IoT Devices
## Using Low-Complexity Machine Learning and Dynamic Scheduling

**Student:** Rishabh Yadav | A00048105  
**Supervisor:** Jennifer McManis  
**Institution:** Dublin City University — School of Electronic Engineering  
**Module:** EEN1095  

## Project Overview
This project investigates whether low-complexity machine learning can 
improve energy efficiency in IoT devices by replacing static duty-cycle 
sleep scheduling with a predictive, adaptive approach.

An ML model is trained to predict future device activity using 
environmental sensor data from the UCI Occupancy Detection dataset. 
Based on the prediction, a dynamic scheduler assigns the device to one 
of three power states: Active, Light Sleep, or Deep Sleep.

## Research Question
Can low-complexity machine learning combined with dynamic scheduling 
increase the energy efficiency of IoT devices relative to static 
duty-cycling, while maintaining acceptable performance?

## Dataset
UCI Occupancy Detection Dataset  
Candanedo, L.M. and Feldheim, V. (2016)  
https://archive.ics.uci.edu/dataset/357/occupancy+detection

## Repository Structure
- `data/` — UCI Occupancy dataset files
- `notebooks/` — Jupyter notebooks for all experiments
- `figures/` — Generated figures and plots
- `src/` — Source modules and helper functions

## Tools and Libraries
Python 3.x, scikit-learn, pandas, numpy, matplotlib

## Status
Week 1 — Project initialisation and repository setup