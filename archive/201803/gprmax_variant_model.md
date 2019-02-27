# gprMax 中实现仿真测线的同时模型也变化的方法

gprMax 中可用指令`src_steps`和`rx_steps`来移动发射天线和接收天线，从而实现测线仿真。但是这种方法并不能实现天线移动时模型也变化的情况
。以下通过一个例子介绍一种通过 python 脚本实现模型变化的方法。

以下是官网的[金属圆柱示例](http://docs.gprmax.com/en/latest/examples_simple_2D.html#b-scan-from-a-metal-cylinder):

![模型](http://docs.gprmax.com/en/latest/_images/cylinder_half_space_geo.png)

    #title: B-scan from a metal cylinder buried in a dielectric half-space
    #domain: 0.240 0.210 0.002
    #dx_dy_dz: 0.002 0.002 0.002
    #time_window: 3e-9

    #material: 6 0 1 0 half_space

    #waveform: ricker 1 1.5e9 my_ricker
    #hertzian_dipole: z 0.040 0.170 0 my_ricker
    #rx: 0.080 0.170 0
    #src_steps: 0.002 0 0
    #rx_steps: 0.002 0 0

    #box: 0 0 0 0.240 0.170 0.002 half_space
    #cylinder: 0.120 0.080 0 0.120 0.080 0.002 0.010 pec

官网给出的运行方法是：

    python -m gprMax user_models/cylinder_Bscan_2D.in -n 60

仿真过程中，模型是没有变化的。现在假如需要天线在运动的同时，金属圆柱上下简谐振荡，则需要每跑一道波形就移动一下金属，不能用标准的B-Scan方法。一种新的思路是：

首先将原配置文件中需要变化的部分用占位符`{x}`代替。

    #title: B-scan from a metal cylinder buried in a dielectric half-space
    #domain: 0.240 0.210 0.002
    #dx_dy_dz: 0.002 0.002 0.002
    #time_window: 3e-9

    #material: 6 0 1 0 half_space

    #waveform: ricker 1 1.5e9 my_ricker
    #hertzian_dipole: z {0} 0.170 0 my_ricker
    #rx: {1} 0.170 0

    #box: 0 0 0 0.240 0.170 0.002 half_space
    #cylinder: 0.120 {2} 0 0.120 {2} 0.002 0.010 pec

其中`{0}`是发射天线的`x`坐标，`{1}`是接收天线的`x`坐标，`{2}`是圆柱圆心的`y`坐标。

以这个文件作为模板，可以用一个 python 脚本生成多个参数不一样的模型文件，并调用 gprmax 执行
