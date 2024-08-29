<template>
	<div class="container">
		<!-- 第一部分 -->
		<div class="header">
			<span id="time"></span>
			<p id="title">新生报到数据可视化平台</p>
			<div class="weather">
				<iframe width="300" scrolling="no" height="25" frameborder="0" allowtransparency="true"
					src="https://i.tianqi.com?c=code&id=10&color=%23FFFFFF&icon=1&py=chongqing&site=12"></iframe>
			</div>
		</div>
		<!-- 第二部分 -->
		<div class="box-two">
			<div class="inner_box1">
				<!-- 今日已报到 -->
				<div class="inner_box1_box">
					<p class="text">今日已报到</p>
					<p class="data">{{ today }}人</p>
				</div>
				<!-- 当前批次已报到 -->
				<div class="inner_box1_box">
					<p class="text">当前批次已报到</p>
					<p class="data">{{batch}}人</p>
				</div>
				<!-- 报到已开始 -->
				<div class="inner_box1_day">
					<p class="text">报到已开始</p>
					<p class="data">{{reportDays}}天</p>
				</div>
				<!-- 报到截止还剩余 -->
				<div class="inner_box1_residue">
					<p class="text">报到截止剩余</p>
					<p class="data">{{reportLeft}}天</p>
				</div>
				<!-- 报告率 -->
				<div class="inner_box1_register">
					<p class="text">报到完成率</p>
					<div id="chartRate" class="chartRate"></div>
				</div>
			</div>
			<div class="inner_box2">
				<div class="num_box">
					<p class="text">住校人数</p>
					<p class="data">{{residents}}人</p>
				</div>
				<div class="num_box">
					<p class="text">宿舍床位入住</p>
					<p class="data">{{bedsOccupied}}床</p>
				</div>
				<div class="num_box">
					<p class="text">宿舍床位总数</p>
					<p class="data">{{bedsTotal}}床</p>
				</div>
				<div class="num_box">
					<p class="text">宿舍床位剩余</p>
					<p class="data">{{bedsVacant}}床</p>
				</div>
			</div>

		</div>
		<!-- 第三部分 -->
		<div class="box-three">
			<div class="box_three_inner1">
				<div class="three_inner1_part1">
					<p class="text">线上与现场报名分布</p>
					<div id="chart1" class="chart1"></div>
				</div>
				<div class="three_inner1_part2">
					<p class="text">各环节进度</p>
					<div id="chart2" class="chart2"></div>
				</div>
			</div>
			<div class="box_three_inner2">
				<p class="text">宿舍入住情况</p>
				<div id="chart3" class="chart3"></div>
			</div>
		</div>
		<!-- 第四部分 -->
		<div class="box-four">
			<div class="box_four_one">
				<p class="text">新生各系人数</p>
				<div id="chart4" class="chart4"></div>
			</div>
			<div class="box_four_two">
				<p class="text">报到人数趋势</p>
				<div id="chart5" class="chart5"></div>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				// 今日报到数据
				today: 310,
				// 当前批次报到数据
				batch: 2686,
				// 报到开始天数
				reportDays: 4,
				// 报到截止剩余天数
				reportLeft: 1,
				// 报到完成率
				rate: 76.67,
				// 住校人数
				residents: 2788,
				// 宿舍床位入住数
				bedsOccupied: 2070,
				// 宿舍床位总数
				bedsTotal: 3000,
				// 宿舍床位剩余数
				bedsVacant: 930,
				// 线上与现场报名分布数据
				online: 100,
				// 各环节进度数据
				progress: [98.01, 97.97, 96.38, 95.68, 94.37],
				// 宿舍入住情况数据
				dorm: {
					floor: [],
					occupied: [],
					vacant: []
				},


				// 新生各系人数数据
				departments: [], // 存储系名
				studentCounts: [], // 存储学生人数
				// 报到人数趋势数据
				trends: {
					online: [520, 202, 60],
					date:[],
					onsite: [0, 0, 0,0]
				}
			}
		},
		created() {
			this.fetchtoday();
			this.fetchCnt();
			this.fetchFacultyData();
			this.fetchRate();
			this.fetchData();
			this.fetchBuildingId();
			this.fetchChart();
		},
		methods: {
			getTime() {
				var date = new Date();
				var year = date.getFullYear();
				var month = (date.getMonth() + 1).toString().padStart(2, '0'); // 月份从1开始，确保两位数
				var day = date.getDate().toString().padStart(2, '0'); // 日期确保两位数
				var hours = date.getHours().toString().padStart(2, '0'); // 小时确保两位数
				var minutes = date.getMinutes().toString().padStart(2, '0'); // 分钟确保两位数
				var seconds = date.getSeconds().toString().padStart(2, '0'); // 秒确保两位数
				var today = date.getDay();

				// 星期的显示
				var days = ["星期日", "星期一", "星期二", "星期三", "星期四", "星期五", "星期六"];
				var dayName = days[today];

				// 拼接完整的时间字符串
				var timeString = `${year}-${month}-${day} ${hours}:${minutes}:${seconds} ${dayName}`;

				document.getElementById("time").innerHTML = timeString

			},
			// 获取今日报到
			fetchtoday() {
				this.axios.get('http://127.0.0.1:8081/getCount')
					.then(response => {
						// 假设后端返回的数据格式是 { code: 0, msg: '成功', data: count }
						if (response.data.code === 200) {
							this.today = response.data.data;
						} else {
							console.error('查询失败:', response.data.msg);
						}
					})
					.catch(error => {
						console.error('请求失败:', error);
					});
			},
			//获取宿舍编号
			fetchBuildingId() {
				this.axios.get('http://127.0.0.1:8081/getBuildId')
					.then(response => {
						if (response.data.code === 200) {
							console.log(response);
							this.dorm.floor = response.data.data.map(item => item.floor);
							this.dorm.occupied = response.data.data.map(item => item.occupied);
							this.dorm.vacant = response.data.data.map(item => item.vacant);

							this.createChart3();
						} else {
							console.error('查询失败:', response.data.msg);
						}
					})
					.catch(error => {
						console.error('请求失败:', error);
					});
			},

			// 获取当前批次
			fetchCnt() {
				this.axios.get('http://127.0.0.1:8081/getBatch')
					.then(response => {
						// 假设后端返回的数据格式是 { code: 0, msg: '成功', data: count }
						if (response.data.code === 200) {
							this.batch = response.data.data;
						} else {
							console.error('查询失败:', response.data.msg);
						}
					})
					.catch(error => {
						console.error('请求失败:', error);
					});
			},
			// 获取各系学生人数数据
			fetchFacultyData() {
				this.axios.get('http://localhost:8081/getInsCnt')
					.then(response => {
						if (response.data.code === 200) {
							// 提取并格式化数据
							this.departments = response.data.data.map(item => item.institute);
							this.studentCounts = response.data.data.map(item => item.student_count);
							// 更新图表
							this.createChart4();
						} else {
							console.error('查询失败:', response.data.msg);
						}
					})
					.catch(error => {
						console.error('请求失败:', error.response ? error.response.data : error.message);
					});
			},
			//获取宿舍信息
			fetchData() {
				this.axios.get('http://127.0.0.1:8081/getDormInfo')
					.then(response => {
						if (response.data.code === 200) {
							// 提取并格式化数据
							this.bedsOccupied = response.data.data.occupiedBeds;
							this.bedsVacant = response.data.data.availableBeds;
							this.bedsTotal = response.data.data.totalBeds;
							this.residents = response.data.data.occupiedBeds;
						} else {
							console.error('查询失败:', response.data.msg);
						}
					})
					.catch(error => {
						console.error('请求失败:', error.response ? error.response.data : error.message);
					});
			},
			// 报到完成率
			fetchRate() {
				this.axios.get('http://127.0.0.1:8081/getRate')
					.then(response => {
						// 假设后端返回的数据格式是 { code: 0, msg: '成功', data: count }
						if (response.data.code === 200) {
							this.rate = (response.data.data) * 100;
							this.createRateChart();
						} else {
							console.error('查询失败:', response.data.msg);
						}
					})
					.catch(error => {
						console.error('请求失败:', error);
					});
			},
			// 获取每日报道人数
			fetchChart() {
				this.axios.get('http://127.0.0.1:8081/getRen')
					.then(response => {
						// 假设后端返回的数据格式是 { code: 0, msg: '成功', data: count }
						if (response.data.code === 200) {
							this.trends.date = response.data.data.map(item => item.date);
							this.trends.online = response.data.data.map(item => item.count);
							this.createChart5();
						} else {
							console.error('查询失败:', response.data.msg);
						}
					})
					.catch(error => {
						console.error('请求失败:', error);
					});
			},
			createRateChart() {
				// 报到完成率 (圆形百分比图)
				var chartRate = this.$echarts.init(document.getElementById('chartRate'));
				var optionRate = {
					series: [{
						type: 'pie',
						radius: ['40%', '50%'],
						center: ['49%', '40%'],
						label: {
							normal: {
								position: 'outside',
								formatter: function(params) {
									// 只显示完成率扇区的标签
									return params.value + '%' + (params.name === '完成率' ? '\n已完成' :
										'\n未完成');
								},
								fontSize: 10,
								fontWeight: 'bold',
								color: '#fff'
							}
						},
						data: [{
							value: this.rate,
							name: '完成率',
							itemStyle: {
								color: '#3c8dbc' // 蓝色
							}
						}, {
							value: 100 - this.rate,
							name: '未完成',
							itemStyle: {
								color: '#d2d2d2' // 灰色
							}
						}]
					}]
				};
				chartRate.setOption(optionRate);
			},
			createChart1() {
				// 线上与现场报名分布 (饼图)
				var chart1 = this.$echarts.init(document.getElementById('chart1'));
				var option1 = {
					series: [{
						type: 'pie',
						center: ['50%', '46%'],
						radius: ['0%', '63%'],
						label: {
							normal: {
								formatter: '{b}\n{d}%', // 统一设置标签格式为名称和百分比
								fontSize: 10.3,
								fontWeight: 'bold', // 设置字体为加粗,
								color: '#fff' // 设置字体颜色为白色
							}
						},
						data: [{
								value: this.online,
								name: '线上报名',
								itemStyle: {
									color: '#3c8dbc'
								}
							}, // 蓝色
							{
								value: 100 - this.online,
								name: '现场报名',
								itemStyle: {
									color: '#ff7f50'
								}
							} // 红色
						]
					}]
				};
				chart1.setOption(option1);
			},
			createChart2() {
				// 各环节进度 (横条状条形图)
				var chart2 = this.$echarts.init(document.getElementById('chart2'));
				var option2 = {
					grid: {
						top: '5%',
						left: '15%'
					},
					yAxis: {
						type: 'category',
						data: ['现场缴费', '宿舍确认', '照片采集', '班级确认', '物品领取'],
						axisLine: {
							show: false
						},
						axisTick: {
							show: false
						},
						axisLabel: {
							rotate: 0,
							color: '#fff',
							textStyle: { // 设置文本样式
								fontWeight: 'bold', // 字体加粗
							}
						},
					},
					xAxis: {
						type: 'value',
						axisLabel: {
							show: false
						},
						axisLine: {
							show: false
						},
						axisTick: {
							show: false
						},
						splitLine: {
							show: false
						}
					},
					legend: {
						data: ['已办理进度占比'],
						textStyle: {
							color: '#fff',
							fontWeight: 'bold'
						},
						right: 30,
						top: 0,
						icon: 'circle',
						itemWidth: 10,
						itemHeight: 10
					},
					series: [{
						data: this.progress,
						type: 'bar',
						name: '已办理进度占比',
						barWidth: '45%',
						itemStyle: {
							color: '#ff915a'
						},
						label: {
							normal: {
								show: true,
								position: 'inside', // 数字显示在柱子内部
								align: 'center', // 水平居中对齐
								verticalAlign: 'middle', // 垂直居中对齐
								formatter: function(param) {
									return param.value + '%'
								},
								textStyle: {
									color: '#fff', // 文本颜色设置为白色
									fontSize: '13' // 根据需要调整字体大小
								}
							}
						}
					}]
				};
				chart2.setOption(option2);
			},
			createChart3() {
				// 初始化楼层的入住和剩余床位数据

				var chart3 = this.$echarts.init(document.getElementById('chart3'));
				var option3 = {
					grid: {
						top: 20,
						left: 40,
						height: '60%'
					},
					legend: {
						data: ['入住床位', '剩余床位'],
						textStyle: {
							color: '#fff',
							fontWeight: 'bold'
						},
						right: 40,
						top: 0,
					},
					xAxis: {
						type: 'category',
						data: this.dorm.floor.map(floor => floor + "楼"), // 使用处理后的楼层数据
						axisLabel: {
							color: '#fff',
							fontWeight: 'bold'
						}
					},
					yAxis: {
						type: 'value',
						axisLine: {
							show: false
						},
						axisTick: {
							show: false
						},
						splitLine: {
							show: false
						},
						axisLabel: {
							show: false
						},
					},
					series: [{
							name: '入住床位',
							data: this.dorm.vacant, // 使用处理后的入住床位数据
							type: 'bar',
							barWidth: '30%',
							itemStyle: {
								color: '#0000ff'
							},
							label: {
								normal: {
									show: true,
									position: 'inside', // 数字显示在柱子内部
									align: 'center', // 水平居中对齐
									verticalAlign: 'middle', // 垂直居中对齐
									formatter: function(param) {
										// 直接显示数据点的值
										return param.value;
									},
									textStyle: {
										color: '#fff', // 文本颜色设置为白色
										fontSize: 13 // 根据需要调整字体大小
									}
								}
							}
						},
						{
							name: '剩余床位',
							data: this.dorm.occupied, // 使用处理后的剩余床位数据
							type: 'bar',
							barWidth: '30%',
							itemStyle: {
								color: '#ffaa00'
							},
							label: {
								normal: {
									show: true,
									position: 'inside', // 数字显示在柱子内部
									align: 'center', // 水平居中对齐
									verticalAlign: 'middle', // 垂直居中对齐
									formatter: function(param) {
										// 直接显示数据点的值
										return param.value;
									},
									textStyle: {
										color: '#fff', // 文本颜色设置为白色
										fontSize: 13 // 根据需要调整字体大小
									}
								}
							}
						}
					]
				};
				chart3.setOption(option3);
			},
			createChart4() {
				// 新生各系人数 (柱状图)
				var chart4 = this.$echarts.init(document.getElementById('chart4'));
				var option4 = {
					grid: {
						top: 0,
						left: 80
					},
					legend: {
						data: ['学生数'],
						textStyle: {
							color: '#fff',
							fontWeight: 'bold'
						},
						right: 0,
						top: 0,
						icon: 'circle'
					},
					xAxis: {
						type: 'category',
						data: this.departments,
						axisLabel: {
							color: '#fff',
							fontWeight: 'bold'
						},
					},
					yAxis: {
						type: 'value',
						axisLine: {
							show: false
						},
						axisTick: {
							show: false
						},
						splitLine: {
							show: false
						},
						axisLabel: {
							show: false
						}
					},
					series: [{
						data: this.studentCounts,
						type: 'bar',
						name: '学生数',
						barWidth: '40%',
						itemStyle: {
							color: '#3c8dbc' // 蓝色
						},
						label: {
							normal: {
								show: true,
								position: 'inside', // 数字显示在柱子内部
								align: 'center', // 水平居中对齐
								verticalAlign: 'middle', // 垂直居中对齐
								formatter: function(param) {
									// 直接显示数据点的值
									return param.value;
								},
								textStyle: {
									color: '#fff', // 文本颜色设置为白色
									fontSize: 13 // 根据需要调整字体大小
								}
							}
						}
					}]
				};
				chart4.setOption(option4);
			},
			createChart5() {
				// 报到人数趋势 (双折线图)
				var chart5 = this.$echarts.init(document.getElementById('chart5'));
				var option5 = {
					grid: {
						top: 25,
						left: 70
					},
					legend: {
						data: ['线上报名', '现场报名'],
						top: 0,
						right: 30,
						textStyle: {
							color: '#fff',
							fontWeight: 'bold'
						}
					},
					xAxis: {
						type: 'category',
						data: this.trends.date,
						axisLabel: {
							color: '#fff',
							fontWeight: 'bold'
						}
					},
					yAxis: {
						type: 'value',
						axisLabel: {
							color: '#fff'
						}
					},
					series: [{
							name: '现场报名',
							data: this.trends.onsite,
							type: 'line',
							itemStyle: {
								color: '#aaff7f'
							},
							label: {
								normal: {
									show: true,
									position: '50%!',
									formatter: function(param) {
										return param.value;
									},
									textStyle: {
										color: '#ffffff', // 文本颜色设置为白色
										fontSize: 10, // 根据需要调整字体大小
										fontWeight: 'bold'
									}
								}
							}
						},
						{
							name: '线上报名',
							data: this.trends.online,
							type: 'line',
							itemStyle: {
								color: '#ff7f50'
							},
							label: {
								normal: {
									show: true,
									position: '50%!',
									formatter: function(param) {
										return param.value;
									},
									textStyle: {
										color: '#f3c1ff', // 文本颜色设置为白色
										fontSize: 10, // 根据需要调整字体大小
										fontWeight: 'bold'
									}
								}
							}
						}
					]
				};
				chart5.setOption(option5);
			},
			createEchart() {
				this.createRateChart();
				this.createChart1();
				this.createChart2();
				this.createChart3();
				this.createChart4();
				this.createChart5();
			}
		},
		mounted() {
			this.$nextTick(() => {
				this.getTime(); // 初始更新时间
				setInterval(this.getTime, 1000);
				this.createEchart(); // 初始化图表
			});
		}
	}
</script>

<style>
	* {
		margin: 0;
		padding: 0;
	}

	.container {
		width: auto;
		height: auto;
		background: #d6d6d0;
	}

	.header {
		width: 100%;
		height: 60px;
		background: #3f6caf;
	}

	#time {
		color: #fff;
		line-height: 60px;
		margin-left: 50px;
	}

	#title {
		display: inline-block;
		color: #fff;
		font-size: 24px;
		margin-left: 15%;
		line-height: 60px;
	}

	.weather {
		display: inline-block;
		float: right;
		margin-right: 10px;
		line-height: 60px;
	}

	.inner_box1 {
		width: 56%;
		height: 230px;
		float: left;
		margin-top: 8px;
		position: relative;
	}

	.inner_box2 {
		width: 43.5%;
		height: 230px;
		float: right;
		margin-top: 8px;
		display: flex;
		flex-flow: row;
		flex-wrap: wrap;
	}

	.inner_box1_box {
		width: 150px;
		height: 230px;
		background: #3f6caf;
		float: left;
		margin-right: 5px;
		border-radius: 5px;
	}

	.inner_box1_day {
		width: 180px;
		height: 113px;
		background: #3f6caf;
		position: absolute;
		left: 42%;
		border-radius: 5px;
	}

	.inner_box1_residue {
		width: 180px;
		height: 113px;
		background: #3f6caf;
		position: absolute;
		left: 42%;
		top: 51%;
		border-radius: 5px;
	}

	.inner_box1_register {
		width: 250px;
		height: 230px;
		background: #3f6caf;
		position: absolute;
		left: 67%;
		border-radius: 5px;
	}



	.num_box {
		clear: both;
		width: 280.5px;
		height: 112px;
		background: #3f6caf;
		margin-left: 5px;
		margin-top: 3px;
		border-radius: 5px;
	}

	.text {
		color: #fff;
		text-align: center;
		margin-top: 10px;
	}

	.data {
		color: #fff;
		text-align: center;
		font-size: 30px;
		margin-top: 50%;
	}

	.inner_box1_day .data {
		margin-top: 10%;
	}

	.inner_box1_residue .data {
		margin-top: 10%;
	}

	.num_box .data {
		margin-top: 10%;
	}

	.box_three_inner1 {
		width: 745px;
		height: 200px;
		float: left;
		margin-top: 8px;
		position: relative;
	}



	.three_inner1_part1 {
		width: 305.5px;
		height: 200px;
		background: #3f6caf;
		float: left;
		border-radius: 5px;
	}

	.three_inner1_part2 {
		width: 434px;
		height: 200px;
		background: #3f6caf;
		float: left;
		margin-left: 5px;
		margin-right: 0px;
		border-radius: 5px;
	}

	.box_three_inner2 {
		width: 566px;
		height: 200px;
		background: #3f6caf;
		float: left;
		margin-top: 8px;
		margin-left: 6px;
		border-radius: 5px;
	}

	.three_inner1_part1 .text {
		text-align: left;
		margin-left: 2%;
	}

	.three_inner1_part2 .text {
		text-align: left;
		margin-left: 2%;
	}

	.box_three_inner2 .text {
		text-align: left;
		margin-left: 2%;
	}

	.box_four_one {
		width: 746px;
		height: 220px;
		background: #3f6caf;
		float: left;
		margin-right: 0px;
		margin-top: 8px;
		border-radius: 5px;
	}

	.box_four_two {
		width: 566px;
		height: 220px;
		background: #3f6caf;
		float: left;
		margin-top: 8px;
		margin-left: 5px;
		border-radius: 5px;
	}

	.box_four_one .text {
		text-align: left;
		margin-left: 2%;
	}

	.box_four_two .text {
		text-align: left;
		margin-left: 2%;
	}



	.chartRate {
		margin-left: 10px;
		height: 240px;
		width: 240px;
	}

	.chart1 {
		height: 180px;
		width: 300px;
		margin-left: auto;
	}

	.chart2 {
		height: 225px;
		width: 450px;
	}

	.chart3 {
		height: 205px;
		width: 600px;
	}

	.chart4 {
		margin-left: 20px;
		height: 220px;
		width: 700px;
	}

	.chart5 {
		height: 220px;
		width: 600px;
	}
</style>