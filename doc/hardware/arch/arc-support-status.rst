.. _hardware_arch_arc_support_status:

Zephyr support status on ARC processors
#######################################

Overview
********

This page describes current state of Zephyr for ARC processors and some future
plans. Please note that

 * plans are given without exact deadlines
 * software features require corresponding hardware to be present and
   configured the proper way
 * not all the features can be enabled at the same time

Support status
**************

Legend:
**Y** - yes, supported; **N** - no, not supported; **WIP** - Work In Progress;
**TBD** - to be decided


+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
|                                                                     | **Processor families**                                                 |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
|                                                                     | **EM**     | **HS3x/4x** | **VPX**           | **HS5x**   | **HS6x**   |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Port status                                                         | upstreamed | upstreamed  | upstreamed [#f6]_ | upstreamed | upstreamed |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| **Features**                                                                                                                                 |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Closely coupled memories (ICCM, DCCM) [#f1]_                        | Y          | Y           | Y                 | TBD        | TBD        |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Execution with caches - Instruction/Data, L1/L2 caches              | Y          | Y           | Y                 | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Hardware-assisted unaligned memory access                           | Y [#f2]_   | Y           | Y                 | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Regular interrupts with multiple priority levels, direct interrupts | Y          | Y           | Y                 | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Fast interrupts, separate register banks for fast interrupts        | Y          | Y           | TBD               | N          | N          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Hardware floating point unit (FPU)                                  | Y          | Y           | TBD [#f6]_        | TBD        | TBD        |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Symmetric multiprocessing (SMP) support, switch-based               | N/A        | Y           | TBD               | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Hardware-assisted stack checking                                    | Y          | Y           | Y                 | N          | N          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Hardware-assisted atomic operations                                 | N/A        | Y           | Y                 | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| DSP ISA                                                             | Y          | N [#f3]_    | TBD [#f6]_        | TBD        | TBD        |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| DSP AGU/XY extensions                                               | Y          | N [#f3]_    | N/A               | TBD        | TBD        |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Userspace                                                           | Y          | Y           | N                 | TBD        | TBD        |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Memory protection unit (MPU)                                        | Y          | Y           | TBD               | N          | N          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Memory management unit (MMU)                                        | N/A        | N           | TBD               | N          | N          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| SecureShield                                                        | Y          | N/A         | N/A               | N/A        | N/A        |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| Single-thread kernel support [#f5]_                                 | Y          | Y           | Y                 | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| **Toolchains**                                                                                                                               |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| GNU (open source GCC-based)                                         | Y          | Y           | N                 | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| MetaWare (proprietary Clang-based)                                  | Y          | Y           | Y                 | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| **Simulators**                                                                                                                               |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| QEMU (open source) [#f4]_                                           | Y          | Y           | N                 | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+
| nSIM (proprietary, provided by MetaWare Development Tools)          | Y          | Y           | Y                 | Y          | Y          |
+---------------------------------------------------------------------+------------+-------------+-------------------+------------+------------+

Notes
*****

.. [#f1] usage of CCMs is limited on SMP systems
.. [#f2] except the systems with secure features (SecureShield) due to HW
         limitation
.. [#f3] We only support save/restore ACCL/ACCH registers in task's context.
         Rest of DSP/AGU registers save/restore isn't implemented but kernel
         itself does not use these registers. This allows single task per
         core to use DSP/AGU safely.
.. [#f4] QEMU doesn't support all the ARC processor's HW features. For the
         detailed info please check the ARC QEMU documentation
.. [#f5] Single-thread kernel is support only for single core targets
.. [#f6] currently only ARC VPX scalar port is supported. The support of VPX vector pipeline, VCCM,
         STU is not included in this port, and require additional development and / or other runtime
         integration.

VPX Vector Registers
--------------------
Zephyr supports a limited form sharing of the VPX vector registers known as
cooperative sharing. Threads that use these registers must bookend the relevant
sections with calls to :c:func:`arc_vpx_lock` and :c:func:`arc_vpx_unlock` to
control access to this resource.

.. note::
    If the system has multiple CPUs, then it is the responsibility of the
    application developer to both pin the thread to a single CPU before it
    attempts to get the cooperative lock, and not modify the CPU affinity
    while it is waiting for or holding that cooperative lock.

Configuration Options
=====================

The cooperative sharing of the VPX vector registers is selected when
configuration option :kconfig:option:`CONFIG_ARC_VPX_COOPERATIVE_SHARING`
is enabled.
