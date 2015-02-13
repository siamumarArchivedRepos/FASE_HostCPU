TinyGarble
=======

For more detailed:
Ebrahim M. Songhori , Siam U. Hussain, Ahmad-Reza Sadeghi, Thomas Schneider, Farinaz Koushanfar, TinyGarble: Highly Compressed and Scalable Sequential Garbled Circuits." will appear on Security and Privacy (SP), 2015 IEEE Symposium on. IEEE, 2015.


##Manual for Synopsys Design Compiler

1. Compile library [Already done, please skip.]

Go to `netlists/lib/dff_full` and run:

	$ ./compile

_Advanced detailed_: Let's suppose that our_lib.lib is located in /path/to/our_lib.

- Go inside /path/to/our_lib and run: 

	$ lc_shell
	lc_shell> set search_path [concat /path/to/our_lib/]
	lc_shell> read_lib our_lib.lib
	lc_shell> write_lib our_lib -format db
	lc_shell> exit

[Note: commands starting with "lc_shell>" should be called inside `lc_shell`. Please ignore "lc_shell>" for them].

2. Compile a benchmark:

Go inside `/netlist/benchmark`, where benchmark is the name of the function. and run:  

	$ ./compile

You can edit `benchmark.dcsh` file to change the parameter of the function.

_Advanced detailed_: Let's suppose that our_lib.db is compiled and located in /path/to/our_lib and benchmark.v is located in /path/to/benchmark/. 

- Go to /path/to/benchmark/ and run: 

	$ design_vision
	design_vision> elaborate benchmark -architecture verilog -library DEFAULT -update
	design_vision> set_max_area -ignore_tns 0 
	design_vision> set_flatten false -design *
	design_vision> set_structure -design * false
	design_vision> set_resource_allocation area_only
	design_vision> report_compile_options
	design_vision> compile -ungroup_all -boundary_optimization  -map_effort high -area_effort high -no_design_rule
	design_vision> write -hierarchy -format verilog -output benchmark_syn.v
	design_vision> exit

It creates benchmark_syn.v in the current directory. [Note: commands starting with "design_vision>" should be called inside design_vision. Please ignore "design_vision>" for them.]

3.Counting number of gates

You can use `netlists/script/count.sh` to count the number of gates in a verilog file. For /path/to/benchmark/benchmark_syn.v, simply run:

	$ netlists/script/count.sh /path/to/benchmark/benchmark_syn.v
	
##Manual for Yosys

Here is how to compile a verilog file named "benchmark.v" using the custom library "asic_cell.lib". We assume that the files are inside a folder named "Synthesis_yosys-abc" inside the "yosys" directory. The final output will be written in "benchmark_syn.v"

	$ cd ~/yosys
	$ ./yosys
	yosys> read_verilog Synthesis_yosys-abc/benchmark.v
	yosys> hierarchy -check -top benchmark
	yosys> proc; opt; memory; opt; fsm; opt; techmap; opt; 
	yosys> abc -liberty Synthesis_yosys-abc/asic_cell_extended.lib
	yosys> opt
	yosys> write_verilog Synthesis_yosys-abc/benchmark_syn.v
	yosys> exit
	
[Note: commands starting with "yosys>" should be called inside design_vision. Please ignore "yosys>" for them.]

##Manual for JustGarbled

For using JustGrable, the dependency should be installed and JustGarble need to be compiled.

- Dependencies
Download msgpack-c from msgpack.org. Compile it using:

	$ ./bootstrap
	$ ./configure
	$ make 

If you have a root access run `$ sudo make install`. Otherwise add the following scripts to ~/.profile: 

	if [ -n "$LD_LIBRARY_PATH"]; 
	then
		export LD_LIBRARY_PATH=/path/to/msgpack-c/src/.libs
	else
		export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/path/to/msgpack-c/src/.libs
	fi
	export MSGPACK=path/to/msgpack-c/src

[Note: Remember replace /path/to/msgpack-c with the actual msgpack path]. This way, it doesn't need "sudo make install".

- Compile JustGarble

Go to justGarble and make it:

	$ make

And test it:

	$ bin/AESFullTest.out

##TODOs
- Upgrade README.md.
- Add synthesis library.
- Add networking to JustGarble.
- Add OT to JustGarble.
