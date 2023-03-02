语言: [中文](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E7%9B%AE%E5%BD%95) / [English](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/blob/main/README-EN.md#table-of-contents) 


检验评估算法包（HX Evaluate, hxeva）主要目的为满足各类项目中产品检验需求，
即在项目中相应的算法产品（包括但不限于短临雷达产品、数值模式预报产品、人工智能订正产品、融合实况产品等）与真值数据（通常为观测资料）比对评估检验，
以生成满足客户需求的评估检验产品。其次为个例回算与算法人员提供历史指定时间、时段、或对应数据矩阵的检验评估工具。

# 目录
   * [说明文档](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E8%AF%B4%E6%98%8E%E6%96%87%E6%A1%A3)
   * [安装说明](http://221.122.67.135:8005/fanglinwanghao/hxeva#%E5%AE%89%E8%A3%85%E8%AF%B4%E6%98%8E)
   * [使用示例](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E4%BD%BF%E7%94%A8%E7%A4%BA%E4%BE%8B)
   * [规范说明](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E8%A7%84%E8%8C%83%E8%AF%B4%E6%98%8E)
   * [软件许可证](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E8%BD%AF%E4%BB%B6%E8%AE%B8%E5%8F%AF%E8%AF%81)
   * [您的支持](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E6%82%A8%E7%9A%84%E6%94%AF%E6%8C%81)
   * [开发团队](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E5%BC%80%E5%8F%91%E5%9B%A2%E9%98%9F)


## [说明文档](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E7%9B%AE%E5%BD%95)

检验评估工具包使用文档详见[hxeva文档](https://aprildomlin.github.io/hxeva/)


## [安装说明](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E7%9B%AE%E5%BD%95)
    
非root权限需要先创建支持安装环境的虚拟环境，并激活环境
    
    conda create -n your_env_name python=X.X   # create your environment

    conda activate your_env_name               # activate your environment 

进入含setup.py的hxeva目录中，并安装环境

    python setup.py install


## [使用示例](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E7%9B%AE%E5%BD%95)
读取实况数据(做为truth)和预报数据(做为test)
    
    from netCDF4 import Dataset

    path = "../scr/data/O_ERA5_SURF_025KM_2022052612.nc"
    tmp = Dataset(path)
    obs = tmp.variables["pre_3h"][::2, ::2] * 1000

    path = "../scr/data/F_T1279_SURF_050KM_NAFP_2022052500_2022052612.nc"
    tmp = Dataset(path)
    fcst = tmp.variables["pre_3h"][:] * 1000

评分检验

    from hxeva import Score
    
    # 直接调用Score.calc方法进行评分计算
    Score.calc(truth=obs, test=fcst, threshold=[0, 0.1, 3, 10, 20, 50, 70, 300],
           names=["TS", "FAR", "PO", "POD", "acc"])

输出结果:

    level	TS	FAR	PO	POD	acc
    ≥ 0	        1.0000	0.0000	0.0000	1.0000	1.0000
    ≥ 0.1	0.5300	0.3403	0.2706	0.7294	0.7820
    ≥ 3	        0.1166	0.7980	0.7838	0.2162	0.9389
    ≥ 10	0.0364	0.8667	0.9524	0.0476	0.9965
    ≥ 20	0.0000	1.0000	1.0000	0.0000	0.9991
    ≥ 50	9999.0000	9999.0000	9999.0000	9999.0000	1.0000
    ≥ 70	9999.0000	9999.0000	9999.0000	9999.0000	1.0000
    >>> Scores Calculated Done! Result has been saved in "./scores.csv"!


误差检验

    from hxeva import Stats

    # 直接调用Stats.calc方法进行评分计算
    Stats.calc(truth=obs, test=fcst, amin=0, amax=300, names=["RMSE", "MAE", "MBE", "CC", "MAE", "MAPE"])

输出结果:

    RMSE:  1.5448
    MAE:  0.5567
    MBE:  0.0134
    CC:  0.2906
    MAE:  0.5567
    MAPE:  1.2067
    >>>Statistics Calculated Done! Result has been saved in "./stats.csv"!

SAL空间检验

    from hxeva import Space

    # 直接调用Space.calc方法进行评分计算
    Space.calc(truth=obs, test=fcst, threshold=[0.1, 999], min_points=15)

输出结果:

    S:  0.6422
    A: -0.0806
    L:  0.0527
    >>>SAL Calculated Done! Result has been saved in "./space.csv"!



## [规范说明](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E7%9B%AE%E5%BD%95)
本项目的代码 [规范说明](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/blob/main/protocol.md)
主要约束开发人员的Python编码规范和代码注释规范。


## [软件许可证](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E7%9B%AE%E5%BD%95)
本项目的 [软件许可证](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/blob/main/LICENSE)


## [您的支持](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E7%9B%AE%E5%BD%95)

Give a ⭐️ if this project helped you!


## [开发团队](http://221.122.67.135:8005/fanglinwanghao/hxeva/-/tree/main#%E7%9B%AE%E5%BD%95)
* [Linwanghao, Fang](http://221.122.67.135:8005/fanglinwanghao)
