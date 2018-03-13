# gprMax 中实现仿真测线的同时模型也变化的方法

gprMax 中可用指令`src_steps`和`rx_steps`来移动发射天线和接收天线，从而实现测线仿真。但是这种方法并不能实现天线移动时模型也变化的情况
。以下通过一个例子介绍一种通过 python 脚本实现模型变化的方法。

以下是官网的[金属圆柱示例](http://docs.gprmax.com/en/latest/examples_simple_2D.html#b-scan-from-a-metal-cylinder):

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

