> [!NOTE]
> you have to rerun all steps if openLANE is closed
> from day1:
> ```bash
> ./flow.tcl -interactive
> package require openlane 0.9
> prep -design picorv32a
> run_synthesis
> ```

## Floorplanning
- placement of non-standard cells, input & output pads
  - includes decoupling capacitors
- `run_floorplan` (output appears to be different than the video)
- <img width="1453" height="402" alt="image" src="https://github.com/user-attachments/assets/5e5c3878-ee35-4b46-992e-15db9155b7fa" />

### Magic
- `cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs`
- cd into most recent run
- `magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky13BA/libs.tech/magic/sky130A.tech lef read tmp/merged.lef def read results/floorplan/picorv32a.floorplan.def &`
  - make sure to include ampersand
- Fullscreen
- Select whole design with `s`, move to center of screen with `v`
- Zoom selection: left & right mouse click to define box, `z` for zoom
- Select object: hover & press s
  - type `what` in tkcon.tcl window for obj details
- <img width="1853" height="1027" alt="image" src="https://github.com/user-attachments/assets/5353c0ad-2ce1-4800-96cd-e215e4a7a845" />


## Placement and Routing
- placement of standard cells, routing of wires
- 1st step is `run_placement`

### Magic pt 2
- Viewing Placement
- from the runs folder,
- `magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky13BA/libs.tech/magic/sky130A.tech lef read tmp/merged.lef def read results/placement/picorv32a.placement.def &`
- <img width="824" height="814" alt="image" src="https://github.com/user-attachments/assets/f3f12bff-d024-45dd-b209-01d273b1c58d" />


