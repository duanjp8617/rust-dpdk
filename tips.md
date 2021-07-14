# Build workflow:
1. Check that the OS is Unix-based using a compiler flag.
2. Use cc command to find out and save the system include path.
3. Find where dpdk is installed:
    * Make sure that dpdk is installed by checking /usr/local/include/rte_config.h is presetned in the system.
    * Find out and save the system include path.
    * Find out and save the system library path.
    * Use println to inform cargo to watch whether the content of these directories are changed.
    * Use println to inform cargo to watch whether the template files are changed.
4. Find all the dpdk lib from the library path by checking the .a files with librte_ prefix.
5. Include all the dpdk headers inside one dpdk.h file.
    * For some header files with common prefixes, only the one whose file name equals the prefix is included in the dpdk.h.
    * For instance,  there are rte_eal.h, rte_eal_ionterrupts.h, rte_eal_memconfig.h, rte_eal_trace.h in the include directory.
    * For the last three headers, their header name has prefix rte_eal_, and will not appear in dpdk.h file.
6. From selected headers, generate safe version of Rust functions, which directly calls the unsafe ffi function in dpdk.
    * The type of the input arguments and return values of these ffi functions are basic, and hence can be directly wrapped inside a safe rust function.
7. From the selected headers, generate all the static function definitions and the corresponding implementations, because bindgen won't automaticall generate them.
    * The static function definitions are wrapped inside static.h and the implemented in static.c
8. Generate dpdk.rs from static.h, with include path pointing to the system include path and build output path (contains dpdk.h).
9. Generate the lib.rs file which uses corresponding functions from dpdk.rs.
10. Compile the generated source.