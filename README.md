# FPGA Applications on Nautilus

**Mohammad Sada and Elham Khoda**

**Sixth National Research Platform (6NRP) Workshop**

**San Diego Supercomputer Center**

**January 28th, 2025**

Welcome to the hands-on part of the tutorial. If you haven't gone through the slides, please go through them before following the step-by-step guide provided here. The slides are provided in the `slides.pptx` file in this repository.

---

### Step 1: Sign up for Coder
Coder is a web service provided on the Nautilus Cluster that allows users to provision various hardware resources (especially FPGAs) and provision persistent storage without needing to know Kubernetes, how to write YAMLs, or understand the underlying services of the cluster.

For more information about Coder, visit: [Coder Documentation](https://docs.nrp.ai/userdocs/coder/coder/)

Please go to [coder.nrp-nautilus.io](https://coder.nrp-nautilus.io) and sign up for an account. If you already have an account, simply log in and proceed to the next step.

---

### Step 2: Use U55C FPGA Vitis Workflow template on Coder
Once you have signed up and logged into Coder, use the U55C FPGA Vitis Workflow template to start a workspace.
You will need at least 32GB of RAM to follow along with this tutorial.

---

### Step 3: Open your Workspace Terminal
After your workspace starts, you'll have access to multiple options, including noVNC for a remote desktop environment and terminal for command-line interaction.
Please open your workspace terminal to proceed with the steps.

---

### Step 4: Install `libboost`
To ensure that your environment has all the necessary dependencies, please install `libboost`.
Run the following commands to update your package list and install the required library:

```bash
sudo apt-get update
sudo apt-get install libboost-all-dev
```

---

### Step 5: Clone the Repository
Clone this repository from GitHub to your workspace. This repository contains the example applications you will be working with:

```bash
git clone https://github.com/nrp-nautilus/6nrp-fpga
```

This will download the repository to your workspace, enabling you to proceed with the next steps.

---

### Step 6: Build the Application
Before building the example application, ensure that you have the correct environment variables set for Vitis and XRT (Xilinx Runtime).
These variables are necessary for the build process to work correctly:

```bash
source /tools/Xilinx/Vitis/2022.2/settings64.sh
source /opt/xilinx/xrt/setup.sh
```

After setting up the environment, navigate to the `hello_world` directory inside the cloned repository:

```bash
cd 6nrp-fpga/hello_world
```

---

### Step 7: Emulation and Build
You will need to compile the application for different targets: software emulation (SW emulation), hardware emulation (HW emulation), and hardware build (actual FPGA build).

1. **SW Emulation:**
For software emulation, the command below will compile and simulate the application using software-based models:

```bash
make all TARGET=sw_emu PLATFORM=/opt/xilinx/platforms/xilinx_u55c_gen3x16_xdma_3_202210_1/xilinx_u55c_gen3x16_xdma_3_202210_1.xpfm
```

2. **HW Emulation:**
Hardware emulation simulates the FPGA hardware and shows how the application would behave on actual FPGA hardware:

```bash
make all TARGET=hw_emu PLATFORM=/opt/xilinx/platforms/xilinx_u55c_gen3x16_xdma_3_202210_1/xilinx_u55c_gen3x16_xdma_3_202210_1.xpfm
```

3. **HW Build:**
The hardware build will compile the application for actual execution on the FPGA:

```bash
make all TARGET=hw PLATFORM=/opt/xilinx/platforms/xilinx_u55c_gen3x16_xdma_3_202210_1/xilinx_u55c_gen3x16_xdma_3_202210_1.xpfm
```

---

### Step 8: Run the Application
You will now run the application in different modes, depending on the target (software or hardware).

1. **Run on FPGA (Software and Hardware Emulation)**

- **SW Emulation:**
To run the software emulation, set the emulation mode to `sw_emu` and then execute the following command:

```bash
export XCL_EMULATION_MODE=sw_emu
./hello_world_xrt -x ./build_dir.sw_emu.xilinx_u55c_gen3x16_xdma_base_3-0/vadd.xclbin
```

**Expected Output:**
The output should show the simulation of the kernel execution and indicate that the test passed:

```
Open the device0
Load the xclbin sw_emu/vadd.xclbin
Kernel Name: vadd_1, CU Number: 0, Thread creation status: success
Allocate Buffer in Global Memory
synchronize input buffer data to device global memory
Execution of the kernel
Kernel Name: vadd_1, CU Number: 0, State: Start
Kernel Name: vadd_1, CU Number: 0, State: Running
Kernel Name: vadd_1, CU Number: 0, State: Idle
Get the output data from the device
TEST PASSED
```

- **HW Emulation:**
Set the emulation mode to `hw_emu` and run the hardware emulation with the following command:

```bash
export XCL_EMULATION_MODE=hw_emu
./hello_world_xrt -x ./build_dir.hw_emu.u55c_gen3x16_xdma_base_3-0/vadd.xclbin
```

**Expected Output:**
Similar to the software emulation, but this time simulating the actual FPGA hardware execution. The output should indicate the status of the emulation process:

```
Open the device0
Load the xclbin hw_emu/vadd.xclbin
INFO: [HW-EMU 05] Path of the simulation directory : /home/coder/6nrp-fpga/hello_world/prebuilt/.run/13303/hw_emu/device0/binary_0/behav_waveform/xsim
...
TEST PASSED
```

---

### Step 9: Run on the Xilinx Alveo U55C FPGAs

Finally, you will run the application on the actual FPGA hardware.

```bash
./hello_world_xrt -x vadd.xclbin
```

**Expected Output:**
The hardware execution on FPGA will show the final output indicating that the test passed:

```
Open the device0
Load the xclbin hw/vadd.xclbin
Allocate Buffer in Global Memory
synchronize input buffer data to device global memory
Execution of the kernel
Get the output data from the device
TEST PASSED
```
