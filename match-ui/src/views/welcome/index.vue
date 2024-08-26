<template>
	<!-- 主体区域 -->
	<div class="section">
		<el-row gutter="10" style="flex-wrap: wrap; flex-direction: row">
			<!-- 个人信息 -->
			<el-col :span="7">
				<el-card class="box-card person">
					<div slot="header" class="clearfix">
						<span>个人信息</span>
						<el-button style="float: right; padding: 3px 0" type="text">...</el-button>
					</div>
					<img src="../../assets/u169.svg" />
					<ul>
						<li>姓名: 熊初墨</li>
						<li>工号: 2023007</li>
						<li>学院: 软件学院</li>
						<li>上次登录时间: 2023-11-28</li>
						<li>上次登录IP: 192.168.73.100</li>
					</ul>
				</el-card>
			</el-col>
			<!-- 快捷入口 -->
			<el-col :span="17">
				<el-card class="box-card quick">
					<div slot="header" class="clearfix">
						<span>快捷入口</span>
						<el-button style="float: right; padding: 3px 0" type="text">...</el-button>
					</div>
					<ul>
						<router-link to="/college">
							<li>
								<img src="../../assets/icon/学院.png" />
								<span class="text">学院管理</span>
							</li>
						</router-link>
						<li>
							<img src="../../assets/icon/专业.png" />
							<span class="text">专业管理</span>
						</li>
						<li>
							<img src="../../assets/icon/用户管理.png" />
							<span class="text">用户管理</span>
						</li>
						<li>
							<img src="../../assets/icon/奖杯.png" />
							<span class="text">竞赛管理</span>
						</li>
						<li>
							<img src="../../assets/icon/团队管理.png" />
							<span class="text">团队管理</span>
						</li>
						<li>
							<img src="../../assets/icon/gzt_cgwt.png" />
							<span class="text">竞赛报名</span>
						</li>
						<li>
							<img src="../../assets/icon/地区统计-01.png" />
							<span class="text">成绩查询</span>
						</li>
						<li>
							<img src="../../assets/icon/资讯专区.png" />
							<span class="text">通知公告</span>
						</li>
						<li>
							<img src="../../assets/icon/报表统计.png" />
							<span class="text">数据统计</span>
						</li>
	
						<li>
							<img src="../../assets/icon/专业.png" />
							<span class="text">赛项查询</span>
						</li>
						<li>
							<img src="../../assets/icon/作品管理.png" />
							<span class="text">作品管理</span>
						</li>
	
					</ul>
				</el-card>
			</el-col><!-- 快捷菜单end -->
	
			<!-- 日历信息 -->
			<el-col :span="7" style="margin-top: 10px;">
				<div>
					<el-card class="box-card calendar">
						<div slot="header" class="clearfix">
							<span>今日日程</span>
							<el-button style="float: right; padding: 3px 0" type="text">...</el-button>
						</div>
						<el-calendar v-model="value"></el-calendar>
					</el-card>
				</div>
			</el-col>
			<!-- 通知公告 -->
			<el-col :span="17" style="margin-top: 10px;">
				<el-card class="box-card notice">
					<div slot="header" class="clearfix">
						<span>通知公告</span>
						<el-button style="float: right; padding: 3px 0" type="text">查看更多</el-button>
					</div>
					<div class="text">
						<ul>
							<li>
								<span>关于举行第四届全国智慧城市与智能建造大学生创新创业竞赛的通知</span>
								<span>2023-09-29</span>
							</li>
							<li>
								<span>重庆机电职业技术大学关于2023年大学生数学建模竞赛 学生队参赛报名暨国（省）赛参赛队遴选办法的通知</span>
								<span>2023-03-28</span>
							</li>
	
							<li>
								<span>关于举办2022年重庆大学第四届“圕强达人” 信息素养大赛的通知</span>
								<span>2023-03-28</span>
							</li>
	
							<li>
								<span>关于举办2023年商业精英挑战赛国际贸易竞赛重庆市赛的通知</span>
								<span>2023-03-28</span>
							</li>
							<li>
								<span>关于教育部关于组织申报国家教育体制改革试点的通知（教改...</span>
								<span>2023-03-28</span>
							</li>
							<li>
								<span>关于国家禁毒委员会办公室、教育部办公厅关于进一步规范学...</span>
								<span>2023-09-23</span>
							</li>
							<li>
								<span>关于教育部关于组织申报国家教育体制改革试点的通知（教改...</span>
								<span>2023-10-30</span>
							</li>
							<li>
								<span>关于教育部关于组织申报国家教育体制改革试点的通知（教改...</span>
								<span>2023-10-30</span>
							</li>
							
						</ul>
					</div>
				</el-card>
			</el-col>
		</el-row>
	</div>
</template>

<script>
	export default{
		data(){
			return{
				
			}
		}
	}
</script>

<style scoped>
	/* 主体部分 */
	.section {
		height: 96%;
		width: 100%;
		display: flex;
	}
	
	.el-row {
		padding: 10px;
		width: 100%;
	
	}
	
	.box-card {
		width: 100%;
		position: relative;
		height: 260px;
	}
	
	.el-card__header {
		padding: 10px 10px;
		font-size: 14px;
	}
	
	.person img {
		width: 130px;
		margin-top: 15px;
	}
	
	.person ul {
		position: absolute;
		top: 60px;
		left: 45%;
	}
	
	.person ul li {
		padding: 0;
		line-height: 30px;
		list-style: none;
		font-size: 13px;
		color: #2f2f2f;
	}
	
	/* 快捷入口样式 */
	.quick ul {
		margin: -25px;
	
	}
	
	.quick ul li {
		list-style: none;
		margin-left: 28px;
		margin-top: 25px;
		width: 70px;
		height: 70px;
		position: relative;
		cursor: pointer;
		float: left;
	}
	
	.quick ul li img {
		width: 100%;
		height: 70px;
	}
	
	.quick .text {
		position: relative;
		margin: 20px 10px;
		font-size: 12px;
		bottom: 5px;
	}
	
	/* 日历样式 */
	.calendar {
		width: 100%;
		height: 424px;
	
	}
	
	/* 日期样式 */
	/deep/.el-calendar-day {
		height: 40px !important;
		width: 40px !important;
		border: none;
		text-align: center;
		line-height: 20px;
		align-items: center;
		padding: 0;
		font-size: 12px;
	}
	
	/* 日期标题 */
	/deep/.el-calendar__title {
		color: #626262;
		font-size: 13px;
	}
	
	/* 星期标题 */
	/deep/.el-calendar-table th {
		font-size: 13px;
	}
	
	.el-calendar__body {
		padding: 0px;
	}
	
	/* 通知公告样式 */
	.notice{
		height: 100%;
	}
	.notice ul li {
		list-style: none;
		height: 40px;
		line-height: 40px;
		border-bottom: 1px dashed gray;
		font-size: 13px;
		background: url("../../assets/05.png") no-repeat center left;
		background-size: 15px 15px;
		margin-left: 10px;
		cursor: pointer;
		position: relative;
	}
	
	.notice ul li span:first-child {
		display: inline-block;
		margin-left: 20px;
	}
	
	.notice ul li span:last-child {
		display: inline-block;
		position: absolute;
		right: 10px;
	}
	
	.notice ul li:hover {
		color: #00aaff;
	}
</style>