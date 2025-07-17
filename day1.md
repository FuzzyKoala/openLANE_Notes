## Design Preperation Step
> Using `script ~/day1.log` to log terminal session (attached)

- Use `docker` to open the openLANE docker container
- then `./flow.tcl -interactive` to  properly start openLANE
- then `package require openlane 0.9` to ensure openLANE is up to date
- <img width="1617" height="469" alt="image" src="https://github.com/user-attachments/assets/db3687c3-5dd1-487d-84c7-3827c89b0c6e" />

- The design is in `~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a`
  - verilog and net list is in the `src` folder

## Design setup stage
- Initialize project with `prep -design picorv32a`
- Now there should be a `runs` folder in `~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a`
  - <img width="914" height="576" alt="image" src="https://github.com/user-attachments/assets/856d96da-389f-4675-9b3d-0ba5f370aafd" />

## Synthesis
- `run_synthesis` does take a moment to execute
