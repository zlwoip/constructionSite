<template>
  <div class="main-container">
    <div class="table-header-container">
      <el-card :body-style="{padding: '0'}" shadow="never">
        <div class="wrapper">
          <div class="left-wrapper">
            <span class="label">数据检索条件:</span>
            <el-input v-model="query.ip" clearable size="mini" placeholder="请输入交换机IP" style="width: 180px;margin:0 5px" />
            <el-input v-model="query.port" clearable size="mini" placeholder="请输入交换机端口" style="width: 180px;margin:0 5px" />
            <el-input v-model="query.address" clearable size="mini" placeholder="请输入端口对应地址" style="width: 180px;margin:0 5px" />
          </div>
          <div class="flex-sub">
            <el-button type="primary" size="mini" icon="el-icon-magic-stick">过滤</el-button>
            <el-button size="mini" icon="el-icon-refresh-left">重置</el-button>
          </div>
          <div class="right-wrapper">
            <el-button type="primary" size="mini" icon="el-icon-download" @click="loadData">拉取数据</el-button>
            <el-button type="success" size="mini" icon="el-icon-setting" @click="toSetting">监听设置</el-button>
          </div>
        </div>
      </el-card>
    </div>
    <el-card :body-style="{padding: 0}" class="table-container" shadow="never">
      <div class="wrapper">
        <div class="table-card-box">
          <div class="table-card-title">
            <el-row>
              <el-col :span="20" style="padding-left: 18px">
                本次巡检共检测到<span style="color:#191970;"> 126 </span>个环网终端，端口数据<span style="color:#0000CD;"> 428 </span>条，有效数据<span style="color:#008B00;"> 413 </span>条，异常数据<span :class="errorNum?'underline touch':'underline'" :title="errorNum?'点击查看异常清单':''" :style="{color: errorNum?'red':'#00CD66'}" @click="openErrorLog()"> {{ errorNum }} </span>条
              </el-col>
              <el-col :span="4" style="text-align: right">
                <span style="padding-right:18px; font-size: 16px;color: #666">{{ dataTime }}</span>
              </el-col>
            </el-row>
          </div>
          <table>
            <tr class="bk">
              <td>地址</td>
              <td>登录地址</td>
              <td>端口</td>
              <td>对应地址</td>
              <td>接收光功率</td>
              <td>发射光功率</td>
              <td>电源</td>
            </tr>
            <tr>
              <td rowspan="6">IPTV环网-威海7503</td>
              <td rowspan="6">10.253.174.240</td>
              <td>XGE0/0/25</td>
              <td>威海6520</td>
              <td>-5.19</td>
              <td>-1.78</td>
              <td rowspan="6">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/25</td>
              <td>威海6520</td>
              <td>-5.26</td>
              <td>-1.65</td>
            </tr>
            <tr>
              <td>GE0/0/3</td>
              <td>荣成6520</td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
            </tr>
            <tr>
              <td>GE0/0/1</td>
              <td>荣成6520</td>
              <td>-36.95</td>
              <td>-36.95</td>
            </tr>
            <tr>
              <td>XGE2/0/2</td>
              <td>文登6520</td>
              <td class="lowlight">-21.87</td>
              <td>1.03</td>
            </tr>
            <tr>
              <td>XGE2/0/1</td>
              <td>乳山6520</td>
              <td>-1.86</td>
              <td>-1.88</td>
            </tr>
            <tr class="bk">
              <td rowspan="5">IPTV环网-文登6520</td>
              <td rowspan="5">10.253.174.225</td>
              <td>XGE1/0/6</td>
              <td>威海7503</td>
              <td class="lowlight">-24.43</td>
              <td>1.29</td>
              <td rowspan="5">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/22</td>
              <td>荣成6520</td>
              <td class="lowlight">-20.75</td>
              <td>2.06</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/21</td>
              <td>乳山6520</td>
              <td>-16.98</td>
              <td>2.08</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/23</td>
              <td>文登7604</td>
              <td>-7.55</td>
              <td>-6.33</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/24</td>
              <td>文登7604</td>
              <td>-7.71</td>
              <td>-5.98</td>
            </tr>
            <tr>
              <td rowspan="5">IPTV环网-荣成6520</td>
              <td rowspan="5">10.253.174.193</td>
              <td>XGE1/0/1</td>
              <td>威海7503</td>
              <td>2.147484E+07</td>
              <td>2.147484E+07</td>
              <td rowspan="5">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/9</td>
              <td>威海7503</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>XGE1/0/22</td>
              <td>文登6520</td>
              <td>-18.96</td>
              <td>2.12</td>
            </tr>
            <tr>
              <td>XGE1/0/23</td>
              <td>荣成7604</td>
              <td>-5.34</td>
              <td>-1.16</td>
            </tr>
            <tr>
              <td>XGE1/0/24</td>
              <td>荣成7604</td>
              <td>-3.03</td>
              <td>-1.23</td>
            </tr>
            <tr class="bk">
              <td rowspan="4">IPTV环网-乳山6520</td>
              <td rowspan="4">10.253.174.209</td>
              <td>XGE1/0/1</td>
              <td>威海7503</td>
              <td>-5.07</td>
              <td>-1.69</td>
              <td rowspan="4">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/21</td>
              <td>文登6520</td>
              <td class="lowlight">-18.76</td>
              <td>2.27</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/23</td>
              <td>乳山7604</td>
              <td>-3.88</td>
              <td>-3.13</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/24</td>
              <td>乳山7604</td>
              <td>-2.58</td>
              <td>-3.26</td>
            </tr>

            <tr>
              <td rowspan="6">IPTV环网-威海6520</td>
              <td rowspan="6">10.253.174.177</td>
              <td>XGE1/0/1</td>
              <td>威海7503</td>
              <td>-5.32</td>
              <td>-2.25</td>
              <td rowspan="6">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/2</td>
              <td>威海7503</td>
              <td>-5.58</td>
              <td>-1.59</td>
            </tr>
            <tr>
              <td>XGE1/0/23</td>
              <td>万象城5560</td>
              <td>-10.42</td>
              <td>-1.62</td>
            </tr>
            <tr>
              <td>XGE1/0/24</td>
              <td>恒瑞街5560</td>
              <td>-8.5</td>
              <td>-2.58</td>
            </tr>
            <tr>
              <td>XGE1/0/5</td>
              <td>12508</td>
              <td>-5.52</td>
              <td>-5.89</td>
            </tr>
            <tr>
              <td>XGE1/0/6</td>
              <td>12508</td>
              <td>-5.52</td>
              <td>-5.89</td>
            </tr>
            <tr class="bk">
              <td rowspan="4">IPTV环网-万象城5560</td>
              <td rowspan="4">10.253.174.180</td>
              <td>XGE1/0/27</td>
              <td>金凤苑5560</td>
              <td>-13.2</td>
              <td>0.51</td>
              <td rowspan="4">电源异常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/28</td>
              <td>威海6520</td>
              <td>-8.42</td>
              <td>-1.5</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/25</td>
              <td>崮山5130</td>
              <td>-8.38</td>
              <td>-1.57</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/26</td>
              <td>温泉5130</td>
              <td>-7.54</td>
              <td>-3.32</td>
            </tr>
            <tr>
              <td rowspan="2">IPTV环网-金凤苑5560</td>
              <td rowspan="2">10.253.174.179</td>
              <td>XGE1/0/27</td>
              <td>恒瑞街5560</td>
              <td>-5.42</td>
              <td>-2.04</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/28</td>
              <td>万象城5560</td>
              <td>-9.34</td>
              <td>-1.94</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">IPTV环网-恒瑞街5560</td>
              <td rowspan="2">10.253.174.178</td>
              <td>XGE1/0/27</td>
              <td>威海6520</td>
              <td>-7.75</td>
              <td>-2.29</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/28</td>
              <td>金凤苑5560</td>
              <td>-15.07</td>
              <td>1.85</td>
            </tr>
            <tr>
              <td rowspan="5">总前端7604</td>
              <td rowspan="5">10.253.182.97</td>
              <td>XGE1/3/0/33</td>
              <td>高区3000</td>
              <td>-8.14</td>
              <td>-1.65</td>
              <td rowspan="5">暂不支持</td>
            </tr>
            <tr>
              <td>XGE2/5/0/7</td>
              <td>经区3000</td>
              <td>-14.32</td>
              <td>-14.32</td>
            </tr>
            <tr>
              <td>XGE2/5/0/4</td>
              <td>文登7604</td>
              <td class="lowlight">-25.37</td>
              <td>0.74</td>
            </tr>
            <tr>
              <td>XGE1/5/0/4</td>
              <td>荣成7604</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>XGE1/5/0/7</td>
              <td>乳山7604</td>
              <td>-4.75</td>
              <td>-1.78</td>
            </tr>

            <tr class="bk">
              <td rowspan="2">经区东环-崮山5130</td>
              <td rowspan="2">10.253.174.181</td>
              <td>XGE1/0/25</td>
              <td>万象城5560</td>
              <td>-9.98</td>
              <td>-1.03</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/26</td>
              <td>泊于5130</td>
              <td>-14.32</td>
              <td>-1.01</td>
            </tr>
            <tr>
              <td rowspan="2">经区东环-泊于5130</td>
              <td rowspan="2">10.253.174.182</td>
              <td>XGE1/0/25</td>
              <td>崮山5130</td>
              <td>-11.99</td>
              <td>-1.12</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/26</td>
              <td>桥头5130</td>
              <td>-11.48</td>
              <td>-1.04</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">经区东环-桥头5130</td>
              <td rowspan="2">10.253.174.183</td>
              <td>XGE1/0/25</td>
              <td>泊于5130</td>
              <td>网管不上</td>
              <td>网管不上</td>
              <td rowspan="2">电源异常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/26</td>
              <td>草庙子5130</td>
              <td>网管不上</td>
              <td>网管不上</td>
            </tr>
            <tr>
              <td rowspan="2">经区东环-草庙子5130</td>
              <td rowspan="2">10.253.174.184</td>
              <td>XGE1/0/25</td>
              <td>桥头5130</td>
              <td>网管不上</td>
              <td>网管不上</td>
              <td rowspan="2">电源异常</td>
            </tr>
            <tr>
              <td>XGE1/0/26</td>
              <td>汪疃5130</td>
              <td>网管不上</td>
              <td>网管不上</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">经区东环-汪疃5130</td>
              <td rowspan="2">10.253.174.187</td>
              <td>XGE1/0/25</td>
              <td>草庙子5130</td>
              <td>-9.14</td>
              <td>-5.22</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/26</td>
              <td>黄岚5130</td>
              <td>-7.59</td>
              <td>-1.14</td>
            </tr>
            <tr>
              <td rowspan="2">经区东环-黄岚5130</td>
              <td rowspan="2">10.253.174.186</td>
              <td>XGE1/0/25</td>
              <td>汪疃5130</td>
              <td>-9.55</td>
              <td>-4.19</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/26</td>
              <td>苘山5130</td>
              <td>-10.34</td>
              <td>-3.82</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">经区东环-苘山5130</td>
              <td rowspan="2">10.253.174.185</td>
              <td>XGE1/0/25</td>
              <td>黄岚5130</td>
              <td>-36.95</td>
              <td>-1.02</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/26</td>
              <td>温泉5130</td>
              <td>-10.02</td>
              <td>-4.53</td>
            </tr>
            <tr>
              <td rowspan="2">经区东环-温泉5130</td>
              <td rowspan="2">10.253.174.188</td>
              <td>XGE1/0/25</td>
              <td>苘山5130</td>
              <td>-13.71</td>
              <td>0.45</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/26</td>
              <td>万象城5560</td>
              <td>-7.54</td>
              <td>-2.78</td>
            </tr>

            <tr class="bk">
              <td rowspan="2">高区</td>
              <td rowspan="2">10.253.182.98</td>
              <td>XGE1/1/2</td>
              <td>电视台</td>
              <td>-7.7</td>
              <td>-3.6</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/54</td>
              <td>总前端</td>
              <td>-10.58</td>
              <td>-2.22</td>
            </tr>
            <tr>
              <td rowspan="2">东平街</td>
              <td rowspan="2">10.253.182.100</td>
              <td>XGE1/0/30</td>
              <td>电视台</td>
              <td>-9.47</td>
              <td>-2.92</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE2/0/29</td>
              <td>金凤苑</td>
              <td>-4.14</td>
              <td>-2.75</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">金凤苑</td>
              <td rowspan="2">10.253.182.101</td>
              <td>XGE1/0/29</td>
              <td>经区</td>
              <td>-8.86</td>
              <td>-1.48</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/30</td>
              <td>东平街</td>
              <td>-6.54</td>
              <td>-2.57</td>
            </tr>
            <tr>
              <td rowspan="2">电视台</td>
              <td rowspan="2">10.253.182.99</td>
              <td>GE1/0/29</td>
              <td>东平街</td>
              <td>-7.91</td>
              <td>-2.88</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>GE1/0/30</td>
              <td>高区</td>
              <td>-5.9</td>
              <td>-2.96</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">经区</td>
              <td rowspan="2">10.253.182.102</td>
              <td>XGE1/0/29</td>
              <td>总前端</td>
              <td>-9.59</td>
              <td>-2.7</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/30</td>
              <td>金凤苑</td>
              <td>-13.72</td>
              <td>-3.43</td>
            </tr>

            <tr>
              <td rowspan="6">环翠7604</td>
              <td rowspan="6">10.253.182.1</td>
              <td>XGE2/3/0/1</td>
              <td>温泉</td>
              <td>-8.15</td>
              <td>1.05</td>
              <td rowspan="6">暂不支持</td>
            </tr>
            <tr>
              <td>XGE2/3/0/2</td>
              <td>张村</td>
              <td>-7.23</td>
              <td>-3.02</td>
            </tr>
            <tr>
              <td>XGE2/3/0/3</td>
              <td>古陌</td>
              <td>-12.24</td>
              <td>-2.96</td>
            </tr>
            <tr>
              <td>XGE2/5/0/1</td>
              <td>崮山</td>
              <td>-18.01</td>
              <td>0.28</td>
            </tr>
            <tr>
              <td>XGE2/5/0/2</td>
              <td>羊亭</td>
              <td>-11.43</td>
              <td>0</td>
            </tr>
            <tr>
              <td>XGE2/5/0/3</td>
              <td>孙家疃</td>
              <td class="lowlight">-10.95</td>
              <td>0.1</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">崮山</td>
              <td rowspan="2">10.253.182.2</td>
              <td>XGE1/0/29</td>
              <td>环翠</td>
              <td>-13.52</td>
              <td>0.05</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>泊于</td>
              <td>-9.49</td>
              <td>-1.3</td>
            </tr>
            <tr>
              <td rowspan="2">泊于</td>
              <td rowspan="2">10.253.182.3</td>
              <td>XGE1/0/29</td>
              <td>崮山</td>
              <td>-7.28</td>
              <td>-1.06</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>桥头</td>
              <td>-10.76</td>
              <td>-1.02</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">桥头</td>
              <td rowspan="2">10.253.182.4</td>
              <td>XGE1/0/29</td>
              <td>泊于</td>
              <td>-10.25</td>
              <td>-1.08</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>草庙子</td>
              <td>-6.96</td>
              <td>-0.4</td>
            </tr>
            <tr>
              <td rowspan="2">草庙子</td>
              <td rowspan="2">10.253.182.5</td>
              <td>XGE1/0/29</td>
              <td>桥头</td>
              <td>-6.74</td>
              <td>-0.31</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>温泉</td>
              <td>-8.63</td>
              <td>-4.85</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">温泉</td>
              <td rowspan="2">10.253.182.6</td>
              <td>XGE1/0/29</td>
              <td>草庙子</td>
              <td>-10.4</td>
              <td>-3.25</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>环翠</td>
              <td>-13.57</td>
              <td>0.05</td>
            </tr>
            <tr>
              <td rowspan="2">张村</td>
              <td rowspan="2">10.253.182.7</td>
              <td>XGE1/0/29</td>
              <td>环翠</td>
              <td>-8.88</td>
              <td>-2.52</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>初村</td>
              <td>-7.65</td>
              <td>-0.6</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">初村</td>
              <td rowspan="2">10.253.182.8</td>
              <td>XGE1/0/29</td>
              <td>张村</td>
              <td>-15.81</td>
              <td>0.36</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>羊亭</td>
              <td>2.147484E+07</td>
              <td>2.147484E+07</td>
            </tr>
            <tr>
              <td rowspan="2">羊亭</td>
              <td rowspan="2">10.253.182.9</td>
              <td>XGE1/0/29</td>
              <td>初村</td>
              <td>-6.51</td>
              <td>-0.22</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>环翠</td>
              <td>-14.44</td>
              <td>0.3</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">孙家疃</td>
              <td rowspan="2">10.253.182.10</td>
              <td>XGE1/0/29</td>
              <td>环翠</td>
              <td class="lowlight">-10.09</td>
              <td>-0.28</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>合庆</td>
              <td>-11.16</td>
              <td>-2.79</td>
            </tr>
            <tr>
              <td rowspan="2">合庆</td>
              <td rowspan="2">10.253.182.11</td>
              <td>XGE1/0/27</td>
              <td>戚谷疃</td>
              <td>-4.97</td>
              <td>-4.28</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/28</td>
              <td>孙家疃</td>
              <td>-10.88</td>
              <td>-3.31</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">戚谷疃</td>
              <td rowspan="2">10.253.182.12</td>
              <td>XGE1/0/27</td>
              <td>古陌</td>
              <td>-4.72</td>
              <td>-3.64</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/28</td>
              <td>合庆</td>
              <td>-8.15</td>
              <td>-2.29</td>
            </tr>
            <tr>
              <td rowspan="2">古陌</td>
              <td rowspan="2">10.253.182.13</td>
              <td>XGE1/0/27</td>
              <td>环翠</td>
              <td>-13.8</td>
              <td>-2.9</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/28</td>
              <td>戚谷疃</td>
              <td>-7.3</td>
              <td>-1.58</td>
            </tr>

            <tr class="bk">
              <td rowspan="9">乳山10506</td>
              <td rowspan="9">10.253.182.33</td>
              <td>XGE2/0/1</td>
              <td>下初</td>
              <td>-2.68</td>
              <td>-3.45</td>
              <td rowspan="9">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/2</td>
              <td>乳山寨</td>
              <td>-3.97</td>
              <td>-4.27</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/3</td>
              <td>乳山口</td>
              <td>-9</td>
              <td>-3.43</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/4</td>
              <td>RS3000</td>
              <td>-0.84</td>
              <td>-3.45</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/13</td>
              <td>大孤山</td>
              <td>-5.42</td>
              <td>-2.57</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/14</td>
              <td>午级</td>
              <td>-7.4</td>
              <td>1.7</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/15</td>
              <td>徐家</td>
              <td>-7.41</td>
              <td>0.96</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/16</td>
              <td>西馨苑</td>
              <td>-7.1</td>
              <td>-3.09</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/18</td>
              <td>电视塔</td>
              <td>-5.07</td>
              <td>-1.56</td>
            </tr>
            <tr>
              <td rowspan="2">电视塔</td>
              <td rowspan="2">10.253.182.49</td>
              <td>XGE1/0/29</td>
              <td>乳山</td>
              <td>-3.72</td>
              <td>-2.76</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>西馨苑</td>
              <td>-6.7</td>
              <td>-1.68</td>
            </tr>
            <tr class="bk">
              <td>RS3000</td>
              <td>10.253.182.52</td>
              <td>XGE1/0/30</td>
              <td>乳山10506</td>
              <td>-3.39</td>
              <td>-2.16</td>
              <td>电源正常</td>
            </tr>

            <tr>
              <td rowspan="2">下初</td>
              <td rowspan="2">10.253.182.34</td>
              <td>XGE1/0/29</td>
              <td>乳山</td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>冯家</td>
              <td>-8.01</td>
              <td>2</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">冯家</td>
              <td rowspan="2">10.253.182.35</td>
              <td>XGE1/0/29</td>
              <td>下初</td>
              <td>-1.03</td>
              <td>-0.38</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>南黄</td>
              <td>-9.55</td>
              <td>0.7</td>
            </tr>
            <tr>
              <td rowspan="2">南黄</td>
              <td rowspan="2">10.253.182.36</td>
              <td>XGE1/0/29</td>
              <td>冯家</td>
              <td>-5.84</td>
              <td>0.67</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>大孤山</td>
              <td>-13.02</td>
              <td>0.83</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">徐家</td>
              <td rowspan="2">10.253.182.37</td>
              <td>XGE1/0/29</td>
              <td>福如东海</td>
              <td>-5.57</td>
              <td>-0.23</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>乳山</td>
              <td>-8.58</td>
              <td>1.96</td>
            </tr>
            <tr>
              <td rowspan="2">福如东海</td>
              <td rowspan="2">10.253.182.51</td>
              <td>XGE1/0/29</td>
              <td>银滩</td>
              <td>-4.66</td>
              <td>-3.95</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>徐家</td>
              <td>-8.36</td>
              <td>2.19</td>
            </tr>

            <tr class="bk">
              <td rowspan="2">银滩</td>
              <td rowspan="2">10.253.182.38</td>
              <td>XGE1/0/29</td>
              <td>海洋所</td>
              <td>-7.3</td>
              <td>2.2</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>福如东海</td>
              <td>-7.5</td>
              <td>-2.33</td>
            </tr>
            <tr>
              <td rowspan="2">海阳所</td>
              <td rowspan="2">10.253.182.39</td>
              <td>XGE1/0/29</td>
              <td>白沙滩</td>
              <td>-4.33</td>
              <td>2.35</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>银滩</td>
              <td>-8.65</td>
              <td>2.78</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">白沙滩</td>
              <td rowspan="2">10.253.182.40</td>
              <td>XGE1/0/29</td>
              <td>乳山口</td>
              <td>-14.85</td>
              <td>-0.16</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>海洋所</td>
              <td>-1.5</td>
              <td>-0.41</td>
            </tr>
            <tr>
              <td rowspan="2">乳山口</td>
              <td rowspan="2">10.253.182.41</td>
              <td>XGE1/0/29</td>
              <td>乳山</td>
              <td>-12.88</td>
              <td>0.59</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>白沙滩</td>
              <td>-8.27</td>
              <td>0.51</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">乳山寨</td>
              <td rowspan="2">10.253.182.42</td>
              <td>XGE1/0/29</td>
              <td>乳山</td>
              <td>-11.91</td>
              <td>0.9</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>育黎</td>
              <td>-5.52</td>
              <td>0.42</td>
            </tr>
            <tr>
              <td rowspan="2">育黎</td>
              <td rowspan="2">10.253.182.43</td>
              <td>XGE1/0/29</td>
              <td>乳山寨</td>
              <td>-9.31</td>
              <td>0.09</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>诸往</td>
              <td>-9.23</td>
              <td>-1.34</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">诸往</td>
              <td rowspan="2">10.253.182.44</td>
              <td>XGE1/0/29</td>
              <td>育黎</td>
              <td>-13.18</td>
              <td>0.59</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>崖子</td>
              <td>-14.69</td>
              <td>0.56</td>
            </tr>
            <tr>
              <td rowspan="2">崖子</td>
              <td rowspan="2">10.253.182.45</td>
              <td>XGE1/1/1</td>
              <td>诸往</td>
              <td>-6.56</td>
              <td>-3.38</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/1/2</td>
              <td>午级</td>
              <td>-5.93</td>
              <td>2.32</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">午级</td>
              <td rowspan="2">10.253.182.46</td>
              <td>XGE1/0/29</td>
              <td>崖子</td>
              <td>-6.67</td>
              <td>2.04</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>乳山</td>
              <td>-9.15</td>
              <td>1.83</td>
            </tr>
            <tr>
              <td rowspan="2">大孤山</td>
              <td rowspan="2">10.253.182.47</td>
              <td>XGE1/0/29</td>
              <td>南黄</td>
              <td>-10.44</td>
              <td>0.42</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>乳山</td>
              <td>-13.46</td>
              <td>0.1</td>
            </tr>
            <tr class="bk">
              <td>西馨园</td>
              <td>10.253.182.48</td>
              <td>XGE1/0/30</td>
              <td>乳山</td>
              <td>-7.76</td>
              <td>-3.55</td>
              <td>电源正常</td>
            </tr>

            <tr>
              <td rowspan="8">文登</td>
              <td rowspan="8">10.253.182.225</td>
              <td>XGE1/2/0/3</td>
              <td>大水泊</td>
              <td>-12.94</td>
              <td>0.8</td>
              <td rowspan="8">暂不支持</td>
            </tr>
            <tr>
              <td>XGE1/2/0/5</td>
              <td>宋村</td>
              <td>-40</td>
              <td>0.98</td>
            </tr>
            <tr>
              <td>XGE2/3/0/3</td>
              <td>侯家</td>
              <td>-17.28</td>
              <td>0.86</td>
            </tr>
            <tr>
              <td>XGE2/3/0/5</td>
              <td>界石</td>
              <td>-14.03</td>
              <td>1.05</td>
            </tr>
            <tr>
              <td>GE1/3/0/42</td>
              <td>文登农信汇聚</td>
              <td>-7.83</td>
              <td>-5.67</td>
            </tr>
            <tr>
              <td>XGE2/3/0/4</td>
              <td>米山</td>
              <td>-36.95</td>
              <td>-3.23</td>
            </tr>
            <tr>
              <td>XGE1/4/0/4</td>
              <td>铺集</td>
              <td>-12.13</td>
              <td>-12.13</td>
            </tr>
            <tr>
              <td>XGE1/4/0/2</td>
              <td>总前端7604</td>
              <td class="lowlight">-24.94</td>
              <td>1.36</td>
            </tr>

            <tr class="bk">
              <td rowspan="6">40环-文登10506</td>
              <td rowspan="6">10.253.178.225</td>
              <td>FortyGi 0/0/1</td>
              <td>米山6860</td>
              <td>-4.93</td>
              <td>0.06</td>
              <td rowspan="6">电源正常</td>
            </tr>
            <tr class="bk">
              <td>FortyGi 1/0/1</td>
              <td>宋村6860</td>
              <td>-10.14</td>
              <td>0.96</td>
            </tr>
            <tr class="bk">
              <td>XGE 2/0/21</td>
              <td>开发区</td>
              <td>-5.57</td>
              <td>-3.33</td>
            </tr>
            <tr class="bk">
              <td>XGE 2/0/22</td>
              <td>文登营</td>
              <td>-10.14</td>
              <td>-2.5</td>
            </tr>

            <tr class="bk">
              <td>XGE2/0/9</td>
              <td>黄岚</td>
              <td>-6.93</td>
              <td>-1.6</td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/10</td>
              <td>苘山</td>
              <td>-7.39</td>
              <td>-2.79</td>
            </tr>

            <tr>
              <td rowspan="2">40环-米山</td>
              <td rowspan="2">10.253.178.241</td>
              <td>FortyGi 1/0/25</td>
              <td>文登10506</td>
              <td>-7.72</td>
              <td>0.99</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>FortyGi 1/0/26</td>
              <td>泽头6860</td>
              <td>-13.37</td>
              <td>0.13</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">40环-泽头</td>
              <td rowspan="2">10.253.178.232</td>
              <td>FortyGi 1/0/25</td>
              <td>米山6860</td>
              <td>-15.37</td>
              <td>0.97</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>FortyGi 1/0/26</td>
              <td>小观6860</td>
              <td>-7.27</td>
              <td>0.97</td>
            </tr>
            <tr>
              <td rowspan="2">40环-小观</td>
              <td rowspan="2">10.253.178.233</td>
              <td>FortyGi 1/0/25</td>
              <td>泽头6860</td>
              <td>-6.21</td>
              <td>-0.2</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>FortyGi 1/0/26</td>
              <td>南海6860</td>
              <td>-5.92</td>
              <td>0.98</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">40环-南海</td>
              <td rowspan="2">10.253.178.242</td>
              <td>FortyGi 1/0/25</td>
              <td>小观6860</td>
              <td>-4.27</td>
              <td>-0.97</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>FortyGi 1/0/26</td>
              <td>泽库6860</td>
              <td>-12.64</td>
              <td>3.06</td>
            </tr>
            <tr>
              <td rowspan="2">40环-泽库</td>
              <td rowspan="2">10.253.178.246</td>
              <td>FortyGi 1/0/25</td>
              <td>南海6860</td>
              <td>-13.85</td>
              <td>2.91</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>FortyGi 1/0/26</td>
              <td>宋村6860</td>
              <td>-11.58</td>
              <td>-1.11</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">40环-宋村</td>
              <td rowspan="2">10.253.178.231</td>
              <td>FortyGi 1/0/25</td>
              <td>泽库6860</td>
              <td>-9.1</td>
              <td>-0.59</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>FortyGi 1/0/26</td>
              <td>文登10506</td>
              <td>-12.59</td>
              <td>-1.15</td>
            </tr>

            <tr>
              <td rowspan="2">大水泊</td>
              <td rowspan="2">10.253.182.226</td>
              <td>XGE1/0/29</td>
              <td>文登</td>
              <td>-13.24</td>
              <td>-0.34</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>张家产</td>
              <td>-11.49</td>
              <td>0.07</td>
            </tr>
            <tr class="bk">
              <td rowspan="3">张家产</td>
              <td rowspan="3">10.253.182.227</td>
              <td>XGE1/0/29</td>
              <td>大水泊</td>
              <td>-11.07</td>
              <td>-0.15</td>
              <td rowspan="3">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>埠口</td>
              <td>-10.83</td>
              <td>-2.64</td>
            </tr>
            <tr class="bk">
              <td>XGE1/1/1</td>
              <td>高村</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td rowspan="2">开发区</td>
              <td rowspan="2">10.253.182.228</td>
              <td>XGE1/0/29</td>
              <td>文登</td>
              <td>-6.32</td>
              <td>-2.9</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>文登营</td>
              <td>-9.51</td>
              <td>-2.42</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">文登营</td>
              <td rowspan="2">10.253.182.229</td>
              <td>XGE1/0/29</td>
              <td>文登10506</td>
              <td>-12.56</td>
              <td>-2.47</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>开发区</td>
              <td>-10.46</td>
              <td>-2.44</td>
            </tr>
            <tr>
              <td rowspan="2">侯家</td>
              <td rowspan="2">10.253.182.230</td>
              <td>XGE1/0/29</td>
              <td>埠口</td>
              <td>-8.45</td>
              <td>-0.03</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>文登</td>
              <td>-12.57</td>
              <td>-0.03</td>
            </tr>

            <tr class="bk">
              <td rowspan="2">宋村</td>
              <td rowspan="2">10.253.182.231</td>
              <td>XGE1/0/29</td>
              <td>文登</td>
              <td>网管不上</td>
              <td>网管不上</td>
              <td rowspan="2">电源异常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/1/2</td>
              <td>泽库</td>
              <td>网管不上</td>
              <td>网管不上</td>
            </tr>
            <tr>
              <td rowspan="2">泽头</td>
              <td rowspan="2">10.253.182.232</td>
              <td>XGE1/1/2</td>
              <td>米山</td>
              <td>网管不上</td>
              <td>网管不上</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/1/1</td>
              <td>小观</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>

            <tr class="bk">
              <td rowspan="2">小观</td>
              <td rowspan="2">10.253.182.233</td>
              <td>XGE1/2/1</td>
              <td>泽头</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>南海</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td rowspan="2">葛家</td>
              <td rowspan="2">10.253.182.234</td>
              <td>XGE1/0/29</td>
              <td>铺集</td>
              <td>-10.84</td>
              <td>-0.2</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>晒子</td>
              <td>-8.26</td>
              <td>0.01</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">界石</td>
              <td rowspan="2">10.253.182.235</td>
              <td>XGE1/0/29</td>
              <td>晒子</td>
              <td>-10.35</td>
              <td>-0.06</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>文登</td>
              <td>-19.54</td>
              <td>0</td>
            </tr>

            <tr>
              <td rowspan="2">汪疃</td>
              <td rowspan="2">10.253.182.236</td>
              <td>XGE1/0/29</td>
              <td>苘山</td>
              <td>-12.81</td>
              <td>-1.59</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/1/2</td>
              <td>黄岚</td>
              <td>-8.89</td>
              <td>-1.88</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">苘山</td>
              <td rowspan="2">10.253.182.237</td>
              <td>XGE1/0/29</td>
              <td>文登</td>
              <td>-12</td>
              <td>-0.1</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>汪疃</td>
              <td>-10.1</td>
              <td>-2.54</td>
            </tr>
            <tr>
              <td rowspan="2">晒子</td>
              <td rowspan="2">10.253.182.238</td>
              <td>XGE1/0/29</td>
              <td>葛家</td>
              <td>-7.61</td>
              <td>-2.35</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>界石</td>
              <td>-5.54</td>
              <td>-2.43</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">埠口</td>
              <td rowspan="2">10.253.182.239</td>
              <td>XGE1/0/29</td>
              <td>张家产</td>
              <td>-10.07</td>
              <td>-2.52</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>侯家</td>
              <td>-3.05</td>
              <td>-2.49</td>
            </tr>
            <tr>
              <td rowspan="2">黄岚</td>
              <td rowspan="2">10.253.182.240</td>
              <td>XGE1/0/29</td>
              <td>文登</td>
              <td>-8.36</td>
              <td>-2.11</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>汪疃</td>
              <td>-8.22</td>
              <td>-1.15</td>
            </tr>
            <tr class="bk">
              <td>文登农信汇聚</td>
              <td>10.253.182.245</td>
              <td>GE1/0/25</td>
              <td>文登</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
              <td>电源正常</td>
            </tr>

            <tr>
              <td>高村</td>
              <td>10.253.182.243</td>
              <td>XGE1/0/30</td>
              <td>张家产</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
              <td>电源正常</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">泽库</td>
              <td rowspan="2">10.253.182.246</td>
              <td>XGE1/0/29</td>
              <td>宋村</td>
              <td>网管不上</td>
              <td>暂不支持</td>
              <td rowspan="2">电源异常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/1/1</td>
              <td>南海</td>
              <td>网管不上</td>
              <td>网管不上</td>
            </tr>
            <tr>
              <td rowspan="2">南海</td>
              <td rowspan="2">10.253.182.242</td>
              <td>XGE1/0/29</td>
              <td>小观</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>泽库</td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
            </tr>
            <tr class="bk">
              <td rowspan="2">米山</td>
              <td rowspan="2">10.253.182.241</td>
              <td>XGE1/0/29</td>
              <td>文登</td>
              <td>网管不上</td>
              <td>网管不上</td>
              <td rowspan="2">电源异常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>泽头</td>
              <td>网管不上</td>
              <td>网管不上</td>
            </tr>
            <tr>
              <td rowspan="2">铺集</td>
              <td rowspan="2">10.253.183.247</td>
              <td>XGE1/0/29</td>
              <td>葛家</td>
              <td>-8.15</td>
              <td>-2.67</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>文登</td>
              <td>-12.58</td>
              <td>0.05</td>
            </tr>
            <tr class="bk">
              <td>荣成</td>
              <td>10.253.182.65</td>
              <td>XGE2/5/0/4</td>
              <td>威海7604</td>
              <td>-11.2</td>
              <td>-2.19</td>
              <td>暂不支持</td>
            </tr>

            <tr>
              <td rowspan="12">40环-荣成10506</td>
              <td rowspan="12">10.253.178.65</td>
              <td>XGE2/0/19</td>
              <td>大疃3000</td>
              <td>-12.36</td>
              <td>0.02</td>
              <td rowspan="12">电源正常</td>
            </tr>
            <tr>
              <td>XGE2/0/20</td>
              <td>桃园3000</td>
              <td>-15.91</td>
              <td>0.4</td>
            </tr>
            <tr>
              <td>XGE2/0/21</td>
              <td>城西3000</td>
              <td>-10.48</td>
              <td>-0.09</td>
            </tr>
            <tr>
              <td>XGE2/0/22</td>
              <td>市府</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>XGE2/0/3</td>
              <td>上庄</td>
              <td>-10.36</td>
              <td>0</td>
            </tr>
            <tr>
              <td>XGE2/0/5</td>
              <td>东山</td>
              <td>-15.52</td>
              <td>0.06</td>
            </tr>
            <tr>
              <td>XGE2/0/9</td>
              <td>荫子</td>
              <td>-16.57</td>
              <td>0.04</td>
            </tr>
            <tr>
              <td>XGE2/0/10</td>
              <td>寻山</td>
              <td>-3.66</td>
              <td>0.34</td>
            </tr>
            <tr>
              <td>Forty1/0/1</td>
              <td>俚岛</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>Forty0/0/1</td>
              <td>夏庄</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>Forty0/0/2</td>
              <td>腾家</td>
              <td>-10.43</td>
              <td>1.19</td>
            </tr>
            <tr>
              <td>Forty1/0/2</td>
              <td>崂山</td>
              <td>-8.5</td>
              <td>0.86</td>
            </tr>

            <tr class="bk">
              <td rowspan="2">40G环-夏庄6730</td>
              <td rowspan="2">10.253.178.66</td>
              <td>40GE0/0/1</td>
              <td>荣成10506</td>
              <td>-8.7582</td>
              <td>-1.9192</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>40GE0/0/2</td>
              <td>崖西</td>
              <td>-6.9875</td>
              <td>-2.1896</td>
            </tr>
            <tr>
              <td rowspan="2">40G环-港西6730</td>
              <td rowspan="2">10.253.178.67</td>
              <td>XGE0/0/24</td>
              <td>崖西</td>
              <td>-8.8306</td>
              <td>0.0169</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>40GE0/0/2</td>
              <td>成山</td>
              <td>-10.1011</td>
              <td>-0.5443</td>
            </tr>
            <tr class="bk">
              <td rowspan="3">40G环-成山6730</td>
              <td rowspan="3">10.253.178.68</td>
              <td>40GE0/0/1</td>
              <td>港西</td>
              <td>-13.851</td>
              <td>-0.6905</td>
              <td rowspan="3">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE0/0/24</td>
              <td>俚岛</td>
              <td>-11.8111</td>
              <td>0.484</td>
            </tr>
            <tr class="bk">
              <td>XGE0/0/23</td>
              <td>龙须</td>
              <td>网管不上</td>
              <td>网管不上</td>
            </tr>
            <tr>
              <td rowspan="2">40G环-俚岛6730</td>
              <td rowspan="2">10.253.178.91</td>
              <td>XGE0/0/24</td>
              <td>成山</td>
              <td>-8.7582</td>
              <td>0.1745</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>40GE0/0/2</td>
              <td>荣成10506</td>
              <td>-12.4565</td>
              <td>0.016</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">40G环-崖西6730</td>
              <td rowspan="2">10.253.178.89</td>
              <td>40GGE0/0/1</td>
              <td>夏庄</td>
              <td>-8.040999</td>
              <td>-1.1742</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE0/0/24</td>
              <td>港西</td>
              <td>-7.602</td>
              <td>0.5964</td>
            </tr>

            <tr>
              <td rowspan="2">40G环-腾家6730</td>
              <td rowspan="2">10.253.178.76</td>
              <td>40GE0/0/1</td>
              <td>荣成10506</td>
              <td>-9.8758</td>
              <td>-0.8645</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>40GE0/0/2</td>
              <td>王连</td>
              <td>-12.0761</td>
              <td>-0.8592</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">40G环-王连6730</td>
              <td rowspan="2">10.253.178.75</td>
              <td>40GE0/0/1</td>
              <td>腾家</td>
              <td>-13.4872</td>
              <td>-1.2651</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>40GE0/0/2</td>
              <td>斥山</td>
              <td>-7.7288</td>
              <td>-1.747</td>
            </tr>
            <tr>
              <td rowspan="2">40G环-斥山6730</td>
              <td rowspan="2">10.253.178.74</td>
              <td>40GE0/0/1</td>
              <td>王连</td>
              <td>-8.755</td>
              <td>0.3024</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE0/0/24</td>
              <td>宁津</td>
              <td>-14.2829</td>
              <td>0.2193</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">40G环-宁津6730</td>
              <td rowspan="2">10.253.178.73</td>
              <td>40GE0/0/1</td>
              <td>崂山</td>
              <td>-12.2257</td>
              <td>2.4556</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE0/0/24</td>
              <td>斥山</td>
              <td>-13.7572</td>
              <td>0.4474</td>
            </tr>
            <tr>
              <td rowspan="2">40G环-崂山6730</td>
              <td rowspan="2">10.253.178.77</td>
              <td>40GE0/0/1</td>
              <td>荣成10506</td>
              <td>-5.8253</td>
              <td>-1.5292</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>40GE0/0/2</td>
              <td>宁津</td>
              <td>-12.7084</td>
              <td>3.1355</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">石岛</td>
              <td rowspan="2">10.253.182.78</td>
              <td>XGE1/0/29</td>
              <td>黄山</td>
              <td>-11.06</td>
              <td>0.62</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>东山</td>
              <td>-8.35</td>
              <td>0.47</td>
            </tr>
            <tr>
              <td rowspan="2">黄山</td>
              <td rowspan="2">10.253.182.79</td>
              <td>XGE1/0/29</td>
              <td>上庄</td>
              <td>-9.64</td>
              <td>-2.98</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>石岛</td>
              <td>-11.44</td>
              <td>0.43</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">上庄</td>
              <td rowspan="2">10.253.182.80</td>
              <td>XGE1/0/29</td>
              <td>荣成</td>
              <td>-15.24</td>
              <td>0.81</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>黄山</td>
              <td>-10.14</td>
              <td>-3.43</td>
            </tr>
            <tr>
              <td rowspan="2">桃园</td>
              <td rowspan="2">10.253.182.81</td>
              <td>XGE1/0/29</td>
              <td>人和</td>
              <td>-11.56</td>
              <td>0.35</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>荣成</td>
              <td>-16.94</td>
              <td>0</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">人和</td>
              <td rowspan="2">10.253.182.82</td>
              <td>XGE1/0/29</td>
              <td>靖海</td>
              <td>-6.56</td>
              <td>0.21</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>桃园</td>
              <td>-8.01</td>
              <td>-0.1</td>
            </tr>
            <tr>
              <td rowspan="2">靖海</td>
              <td rowspan="2">10.253.182.83</td>
              <td>XGE1/0/29</td>
              <td>虎山</td>
              <td>-6.96</td>
              <td>0.98</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>人和</td>
              <td>-11.6</td>
              <td>1.09</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">虎山</td>
              <td rowspan="2">10.253.182.84</td>
              <td>XGE1/0/29</td>
              <td>大疃</td>
              <td>-12.74</td>
              <td>-0.84</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>靖海</td>
              <td>-3.6</td>
              <td>-0.34</td>
            </tr>
            <tr>
              <td rowspan="2">大疃</td>
              <td rowspan="2">10.253.182.85</td>
              <td>XGE1/0/29</td>
              <td>荣成</td>
              <td>-7.56</td>
              <td>0.7</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>虎山</td>
              <td>-11.42</td>
              <td>-0.08</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">老广电</td>
              <td rowspan="2">10.253.182.86</td>
              <td>XGE1/0/29</td>
              <td>荣成</td>
              <td>-5.39</td>
              <td>-3.49</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>城西</td>
              <td>-4.51</td>
              <td>-2.58</td>
            </tr>
            <tr>
              <td rowspan="2">城西</td>
              <td rowspan="2">10.253.182.87</td>
              <td>XGE1/0/29</td>
              <td>老广电</td>
              <td>-7.38</td>
              <td>-2.47</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>荣成</td>
              <td>-6.92</td>
              <td>-0.43</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">荫子</td>
              <td rowspan="2">10.253.182.88</td>
              <td>XGE1/0/29</td>
              <td>荣成</td>
              <td>-14.02</td>
              <td>0.28</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>埠柳</td>
              <td>-16.28</td>
              <td>-0.73</td>
            </tr>

            <tr>
              <td rowspan="2">埠柳</td>
              <td rowspan="2">10.253.182.90</td>
              <td>XGE1/0/29</td>
              <td>荫子</td>
              <td>-15.12</td>
              <td>0.4</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>马道</td>
              <td>-4.63</td>
              <td>0.07</td>
            </tr>

            <tr class="bk">
              <td rowspan="2">寻山</td>
              <td rowspan="2">10.253.182.92</td>
              <td>XGE1/0/29</td>
              <td>马道</td>
              <td>-8.36</td>
              <td>0.15</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>荣成</td>
              <td>-3.95</td>
              <td>0.32</td>
            </tr>
            <tr>
              <td>龙须</td>
              <td>10.253.182.69</td>
              <td>XGE1/0/29</td>
              <td>成山</td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
              <td>电源正常</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">马道</td>
              <td rowspan="2">10.253.182.70</td>
              <td>XGE1/0/29</td>
              <td>埠柳</td>
              <td>-8.13</td>
              <td>0.54</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>寻山</td>
              <td>-9.07</td>
              <td>0.11</td>
            </tr>
            <tr>
              <td>市府</td>
              <td>10.253.182.71</td>
              <td>GE1/0/25</td>
              <td>荣成</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
              <td>电源正常</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">东山</td>
              <td rowspan="2">10.253.182.72</td>
              <td>XGE1/0/29</td>
              <td>石岛</td>
              <td>-9.05</td>
              <td>0.38</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>荣成</td>
              <td>-11.1</td>
              <td>-0.58</td>
            </tr>

            <tr>
              <td rowspan="7">金凤苑汇聚</td>
              <td rowspan="7">10.253.182.108</td>
              <td>XGE1/0/29</td>
              <td>总前端12508</td>
              <td>-6.23</td>
              <td>-2.45</td>
              <td rowspan="7">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>总前端12508</td>
              <td>-9.19</td>
              <td>-1.51</td>
            </tr>

            <tr>
              <td>GE1/0/23</td>
              <td>龙泽府5800</td>
              <td>电口</td>
              <td>电口</td>
            </tr>
            <tr>
              <td>GE1/0/27</td>
              <td>龙泽府6800</td>
              <td>-0.83</td>
              <td>-6.68</td>
            </tr>
            <tr>
              <td>GE1/1/6</td>
              <td>龙泽府6800</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>GE1/0/24</td>
              <td>湾九里5800</td>
              <td>电口</td>
              <td>电口</td>
            </tr>
            <tr>
              <td>GE1/0/26</td>
              <td>湾九里5800</td>
              <td>-40</td>
              <td>-40</td>
            </tr>

            <tr class="bk">
              <td rowspan="19">12508</td>
              <td rowspan="19">10.253.174.21</td>
              <td>XGE3/0/1</td>
              <td>高区汇聚电视台方向</td>
              <td>-8.43</td>
              <td>-2.6</td>
              <td rowspan="19">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE7/0/1</td>
              <td>高区汇聚高区方向</td>
              <td>-7.87</td>
              <td>-7.87</td>
            </tr>
            <tr class="bk">
              <td>XGE9/0/2</td>
              <td>金凤苑汇聚金凤苑方向</td>
              <td>-7.34</td>
              <td>-2.51</td>
            </tr>
            <tr class="bk">
              <td>XGE3/0/2/</td>
              <td>金凤苑汇聚威高方向</td>
              <td>-9.95</td>
              <td>-1.5</td>
            </tr>
            <tr class="bk">
              <td>XGE7/0/8</td>
              <td>经区汇聚金凤苑方向</td>
              <td>-9.93</td>
              <td>0.28</td>
            </tr>
            <tr class="bk">
              <td>XGE3/0/8/</td>
              <td>经区汇聚经区方向</td>
              <td>-7.02</td>
              <td>-0.15</td>
            </tr>

            <tr class="bk">
              <td>GE5/0/36</td>
              <td>威海6520</td>
              <td>-6.2</td>
              <td>-6.2</td>
            </tr>
            <tr class="bk">
              <td>GE5/0/42</td>
              <td>威海6520</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr class="bk">
              <td>XGE9/0/4</td>
              <td>CR16K</td>
              <td>-2.31</td>
              <td>-3.22</td>
            </tr>

            <tr class="bk">
              <td>GE5/0/19</td>
              <td>东河北5800-1</td>
              <td>-9.83</td>
              <td>-5.97</td>
            </tr>
            <tr class="bk">
              <td>GE5/0/20</td>
              <td>东河北5800-1</td>
              <td>-11.77</td>
              <td>-5.79</td>
            </tr>
            <tr class="bk">
              <td>GE5/0/7</td>
              <td>东河北5800-2</td>
              <td>-12.78</td>
              <td>-6.23</td>
            </tr>
            <tr class="bk">
              <td>GE5/0/29</td>
              <td>东河北5800-2</td>
              <td>-11.22</td>
              <td>-4.85</td>
            </tr>

            <tr class="bk">
              <td>GE5/0/10</td>
              <td>威高广场5800-1</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr class="bk">
              <td>GE5/0/25</td>
              <td>威高广场5800-1</td>
              <td>-35.64</td>
              <td>-30.92</td>
            </tr>
            <tr class="bk">
              <td>GE5/0/8</td>
              <td>威高广场5800-2</td>
              <td>-40</td>
              <td>-40</td>
            </tr>
            <tr class="bk">
              <td>GE5/0/21</td>
              <td>威高广场5800-2</td>
              <td>-36.95</td>
              <td>-36.95</td>
            </tr>
            <tr class="bk">
              <td>XGE3/0/6</td>
              <td>望岛6800经区方向</td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
              <td>Error in SNMP reply. Error 2 index 1
              </td>
            </tr>
            <tr class="bk">
              <td>XGE2/0/6</td>
              <td>望岛6800中信苑方向</td>
              <td>-33.6</td>
              <td>-1.3</td>
            </tr>

            <tr>
              <td rowspan="15">高区汇聚</td>
              <td rowspan="15">10.253.182.106</td>
              <td>XGE1/0/25</td>
              <td>总前端12508</td>
              <td>-7.67</td>
              <td>-2.48</td>
              <td rowspan="15">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/26</td>
              <td>总前端12508</td>
              <td>-7.57</td>
              <td>-3.07</td>
            </tr>
            <tr>
              <td>GE1/0/17</td>
              <td>高区</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>GE1/0/1</td>
              <td>后峰西5800</td>
              <td>-12.61</td>
              <td>-5.94</td>
            </tr>
            <tr>
              <td>GE1/0/2</td>
              <td>后峰西5800</td>
              <td>-8.92</td>
              <td>-5.14</td>
            </tr>
            <tr>
              <td>GE1/0/19</td>
              <td>东涝台5800</td>
              <td>-12.54</td>
              <td>-7.28</td>
            </tr>
            <tr>
              <td>GE1/0/20</td>
              <td>东涝台5800</td>
              <td>-15.51</td>
              <td>-6.34</td>
            </tr>
            <tr>
              <td>GE1/0/7</td>
              <td>西涝台5800</td>
              <td>-10.27</td>
              <td>-5.23</td>
            </tr>
            <tr>
              <td>GE1/0/8</td>
              <td>西涝台5800</td>
              <td>-8.31</td>
              <td>-5.31</td>
            </tr>
            <tr>
              <td>GE1/0/21</td>
              <td>高区老机房5800-1</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>GE1/0/22</td>
              <td>高区老机房5800-1</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>GE1/0/3</td>
              <td>高区老机房5800-2</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>GE1/0/4</td>
              <td>高区老机房5800-2</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr>
              <td>GE1/0/5</td>
              <td>高区老机房5800-3</td>
              <td>-5.3</td>
              <td>-4.81</td>
            </tr>
            <tr>
              <td>GE1/0/6</td>
              <td>高区老机房5800-3</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>

            <tr class="bk">
              <td rowspan="7">经区汇聚</td>
              <td rowspan="7">10.253.182.107</td>
              <td>GE1/0/24</td>
              <td>经区3000</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
              <td rowspan="7">电源正常</td>
            </tr>
            <tr class="bk">
              <td>GE1/0/18</td>
              <td>经区5800-1</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr class="bk">
              <td>GE1/0/23</td>
              <td>经区5800-1</td>
              <td>-5.97</td>
              <td>-5.18</td>
            </tr>
            <tr class="bk">
              <td>GE1/0/19</td>
              <td>经区5800-2</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr class="bk">
              <td>GE1/0/20</td>
              <td>经区5800-2</td>
              <td>-5.79</td>
              <td>-5.19</td>
            </tr>
            <tr class="bk">
              <td>GE1/0/16</td>
              <td>经区5800-4</td>
              <td>暂不支持</td>
              <td>暂不支持</td>
            </tr>
            <tr class="bk">
              <td>GE1/0/17</td>
              <td>经区5800-4</td>
              <td>-7.46</td>
              <td>-5.95</td>
            </tr>

            <tr>
              <td rowspan="6">万象城</td>
              <td rowspan="6">10.253.182.209</td>
              <td>XGE1/2/0/26</td>
              <td>阳光海上城</td>
              <td>-5.19</td>
              <td>-2.48</td>
              <td rowspan="6">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/3/0/25</td>
              <td>凤林</td>
              <td>-6.91</td>
              <td>-1.43</td>
            </tr>
            <tr>
              <td>XGE2/2/0/26</td>
              <td>夏家疃</td>
              <td>-8.36</td>
              <td>-1.83</td>
            </tr>
            <tr>
              <td>XGE2/3/0/25</td>
              <td>南曲阜</td>
              <td>-6.92</td>
              <td>-1.42</td>
            </tr>
            <tr>
              <td>XGE1/2/0/27</td>
              <td>总前端</td>
              <td>-10</td>
              <td>-2.74</td>
            </tr>
            <tr>
              <td>XGE2/2/0/27</td>
              <td>总前端</td>
              <td>-12.15</td>
              <td>-2.48</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">夏家疃</td>
              <td rowspan="2">10.253.182.218</td>
              <td>XGE1/0/29</td>
              <td>万象城</td>
              <td>-6.95</td>
              <td>-2.71</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>徐家疃</td>
              <td>-18.21</td>
              <td>-2.66</td>
            </tr>
            <tr>
              <td rowspan="2">徐家疃</td>
              <td rowspan="2">10.253.182.219</td>
              <td>XGE1/0/27</td>
              <td>夏家疃</td>
              <td>-13.54</td>
              <td>-1.35</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/28</td>
              <td>阳光海上城</td>
              <td>-13.81</td>
              <td>-2.41</td>
            </tr>

            <tr class="bk">
              <td rowspan="2">阳光海上城</td>
              <td rowspan="2">10.253.182.210</td>
              <td>XGE1/0/29</td>
              <td>徐家疃</td>
              <td>-12.81</td>
              <td>-1.56</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/30</td>
              <td>万象城</td>
              <td>-5.35</td>
              <td>-1.93</td>
            </tr>
            <tr>
              <td rowspan="2">华新家园</td>
              <td rowspan="2">10.253.182.215</td>
              <td>XGE1/0/29</td>
              <td>南曲阜</td>
              <td>-4.93</td>
              <td>-1.27</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE1/0/30</td>
              <td>凤林</td>
              <td>-8.23</td>
              <td>-1.3</td>
            </tr>
            <tr class="bk">
              <td rowspan="2">南曲阜</td>
              <td rowspan="2">10.253.182.216</td>
              <td>XGE1/0/27</td>
              <td>万象城</td>
              <td>-12.7</td>
              <td>-2.37</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr class="bk">
              <td>XGE1/0/28</td>
              <td>华新家园</td>
              <td>-4.73</td>
              <td>-1.5</td>
            </tr>
            <tr>
              <td rowspan="2">凤林</td>
              <td rowspan="2">10.253.182.214</td>
              <td>XGE2/0/29</td>
              <td>华新园</td>
              <td>-5.85</td>
              <td>-2.68</td>
              <td rowspan="2">电源正常</td>
            </tr>
            <tr>
              <td>XGE2/0/30</td>
              <td>万象城</td>
              <td>-7.7</td>
              <td>-0.96</td>
            </tr>
          </table>
        </div>
      </div>
    </el-card>
    <error-log ref="errorLogPage" />
    <setting-page ref="settingPage" />
  </div>
</template>

<script>
import errorLog from './errorLog'
import settingPage from './setting'
export default {
  components: { errorLog, settingPage },
  data() {
    return {
      dataTime: '',
      errorNum: 3,
      query: {
        ip: '',
        port: '',
        address: ''
      },
      tableList: [
        {
          name: 'IPTV环网-威海7503',
          ip: '10.253.174.240',
          errorNum: 0,
          portList: [
            { port: 'XGE0/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/3', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/1', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/2', address: '文登6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/1', address: '乳山6520', input: -5.19, output: -1.78 }
          ]
        }, {
          name: 'IPTV环网-文登6520',
          ip: '10.253.174.225',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/6', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/22', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/21', address: '乳山6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/23', address: '文登7604', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/24', address: '文登7604', input: -5.19, output: -1.78 }
          ]
        }, {
          name: 'IPTV环网-荣成6520',
          ip: '10.253.174.193',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/1', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/9', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/22', address: '文登6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/23', address: '荣成7604', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/24', address: '荣成7604', input: -5.19, output: -1.78 }
          ]
        }
      ]
    }
  },
  mounted() {
    this.dataTime = this.getNowDateTime()
  },
  methods: {
    // 获取当前时间
    getNowDateTime(now) {
      if (!now) {
        now = new Date()
      }
      return `${now.getFullYear()}-${(now.getMonth() + 1).toString().padStart(2, '0')}-${now.getDate().toString().padStart(2, '0')} ${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}:${now.getSeconds().toString().padStart(2, '0')}`
    },
    openErrorLog() {
      if (this.errorNum) {
        this.$refs.errorLogPage.loadData()
      }
    },
    search() {},
    resetSearch() {
      this.query.ip = ''
      this.query.port = ''
      this.query.address = ''
    },
    toSetting() {
      this.$refs.settingPage.loadData()
    },
    loadData() {

    }
  }
}
</script>

<style lang="scss" scoped>
.table-card-box {
  .table-card-title {
    width: 100%;
    padding: 2px;
    margin-bottom: 10px;
    color: #4e4e4e;
    font-weight: bold;
    font-size: 14px;
    background-color: rgba(0,0,0,0.1);
    span {
      font-size: 18px;
    }
  }
  table {
    border-collapse: collapse;
    width: 100%;
    margin-bottom: 20px;
    text-align: left;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  }
  th, td {
    border: 1px solid #ddd;
    padding: 8px;
    border-radius: 5px;
    text-align: center;
  }
  th {
    background-color: #e2e2e2;
    font-weight: bold;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  }
  .bk {
    background-color: #f2f2f2;
  }
}

.table-header-container {
  .wrapper {
    height: 50px;
    display: flex;
    flex-direction: row;
    align-items: center;

    .left-wrapper {
      margin-left: 15px;
    }

    .flex-sub {
      padding: 0 20px;
    }

    .right-wrapper {
      margin-right: 15px;
    }

    .label {
      padding: 0 10px;
    }
  }

  .search-content-wrapper {
    padding: 10px;

    .search-item-wrapper {
      justify-content: center;
      align-items: center;

      & > span {
        color: #606266;
        font-size: 14px;
        font-weight: 500;
        margin-right: 8px;
      }

      .form-item {
        flex: 1;
      }
    }
  }
}

.table-container {
  .wrapper {
    height: 70vh;
    padding: 4px 20px;
    overflow-y: auto;
  }
}
</style>
