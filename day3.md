> [!NOTE]
> So far, commands have been
> ```bash
> ./flow.tcl -interactive
> package require openlane 0.9
> prep -design picorv32a
> run_synthesis
> run_floorplan
> run_layout
> ```

## IO Placer Settings
- can adjust where input output is on the die
- use `set ::env(FP_IO_MODE) [number]`
    - 0 = matching = random equidistant, 1 = random equidistant, 2 = "hungarian"
    - hungarian: <img width="831" height="843" alt="image" src="https://github.com/user-attachments/assets/0be98ecf-ba81-4d24-be6f-0cd5b9526f1a" />

## SPICE Deck
> [Cheatsheet](https://web.stanford.edu/class/ee133/handouts/general/spice_ref.pdf)
1) draw out circuit schematic
2) label nodes: put nodes such that each between two nodes there is exactly 1 component
3) add netlist description to deck
   - tranistors: `name drainnode gatenode substratenode sourcenode pmos|nmos W=channelwidth L=channellength`
   - capacitors: `name innode outnode capacitance`
   - voltage sources: `name innode outnode value`
4) add simulation commands
   - `.op` to mark "operating point"
   - `.dc voltage_source start stop step` to define analyisis type as step function
   - `.tran .TRAN TSTEP TSTOP <TSTART <TMAX>>` to define analysis type as wave
     - TSTEP: printing increment; - TSTOP: final time; - TSTART: starting time; - TMAX: maximum step size; 
   - `.LIB "path/to/library.mod name` to define library
   - `.end`

## SPICE Inverter Lab
1) `git clone https://github.com/nickson-jose/vsdstdcelldesign.git` into `~/Desktop/work/tools/openlane_working_dir/openlane`
2) copy tech file over: from `~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic`, `cp sky130A.tech ~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign`
3) run magic to see inverter: `magic -T sky130A.tech sky130_inv.mag &`
    - after selecting, `what` also shows layer that selection is on
    - `s` multiple times also selects what selection is connected to
    - `shift+s` adds to current selection
    - in Drc tab, `DRC Find next error` zooms to error and shows details in console
    - `shift+z` unzooms
4) extract net list (in console:)
   - `extract all` to make .ext file
   - `ext2spice cthresh 0 rthresh 0` to include all parasitics, then `ext2spice` to export
   - <img width="459" height="826" alt="image" src="https://github.com/user-attachments/assets/dd1f2ff4-01f8-4aae-a113-b68255c38906" />
5) modify extracted file:
   ```
   * SPICE3 file created from sky130_inv.ext - technology: sky130A
    
   .option scale=0.01u
   .include ./libs/pshort.lib
   .include ./libs/nshort.lib
    
   //.subckt sky130_inv A Y VPWR VGND
   M1000 Y A VGND VGND nshort_model.0 ad=1.44n pd=0.152m as=1.37n ps=0.148m w=35 l=23
   M1001 Y A VPWR VPWR pshort_model.0 ad=1.44n pd=0.152m as=1.52n ps=0.156m w=37 l=23
   VDD VPWR 0 3.3V
   VSS VGND 0 0V
   VA A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
   C0 VPWR A 0.0774f
   C1 Y VPWR 0.117f
   C2 Y A 0.0754f
   C3 Y VGND 0.279f
   C4 A VGND 0.45f
   C5 VPWR VGND 0.781f
    
   //.ends
    
   .tran 1n 20n
    
   .control
   run
   .endc
   ```
6) use `ngspice path/to/deck.spice` (install with `sudo apt get ngspice`)
   - `plot y vs time a`
   - <img width="1669" height="655" alt="image" src="https://github.com/user-attachments/assets/abe9b56e-cbd8-4051-bf93-be68e843a658" />
   - change load cap value to reduce output spikes
     - edit line 16 `C3 Y VGND 0.279f` --> `C3 Y VGND 2f`
     - <img width="1452" height="936" alt="image" src="https://github.com/user-attachments/assets/bebc33f0-5a06-42c6-9a67-e4cca20c4725" />
   - zoom box by dragging right click, left click reports selection location in console
   - finding rise transition (same procedure for fall)
     - time from when output is 20% of vdd to 80% of vdd
     - here 60.76 picoseconds
   - finding propagation delay (or cell rise delay?) (same procedure for fall)
     - time from when input crosses 50% to when output crosses 50% of vdd
     - here 57.0259 picoseconds


## Sky130 DRC Magic Lab
  - get pdk: in ~, `wget opencircuitdesign.com/open_pdks/archive/drc_tests.tgz`
 - cd to drc_tests
 - run `magic -d XR &`, File>Open>met3.mag
 - Keyboard Shortcuts / Commands
   - use `;` to temporarily focus on console, focus returns on enter
   - `drc why` shows drc error
   - `cif see VIA2` shows hidden via2 layer
   - `snap int` to snap selection to 5nm grid
   - `b` shows dimensions of selections
   - `load [filename]`
   - `tech load [filename.tech]` to reload tech file
     - after loading, must run `drc check`
   - `drc style [drc(full)|drc(fast)]` to change pedanticism 

### Lab Results
 - <img width="821" height="313" alt="image" src="https://github.com/user-attachments/assets/54084d88-ff68-45e2-a37d-199fce0f7724" />
 - <img width="631" height="364" alt="image" src="https://github.com/user-attachments/assets/5c499556-a3d7-4101-a1a5-50d54fad692b" />
 - <img width="1544" height="842" alt="image" src="https://github.com/user-attachments/assets/345d912f-91d9-4027-a671-b002371b72c4" />
 - 
   - `cifmaxwidth dnwell_pnp 0 bend_illegal \ "Deep N-well cannot overlap pnp:dg (dnwell.9)"`
   - `templayer dnwell_pnp dnwell` `and pnp`