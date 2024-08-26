<template>
	<div>
		<el-row>
			<!-- 操作栏 -->
			<el-col :span="24">
				<el-card class="box-card">
					<el-input v-model="input" placeholder="请输入通知标题" size="small" class="filter-input"></el-input>
					<el-input v-model="input" placeholder="请输入时间" size="small" class="filter-input"></el-input>
					<el-button type="primary" icon="el-icon-search" size="small" plain>搜索</el-button>
					<el-button type="primary" icon="el-icon-refresh-right" size="small">重置</el-button><br />

					<el-button type="primary" size="small" icon="el-icon-plus" class="button" @click="handleCreate()"
						plain>新增</el-button>
					<el-button type="success" size="small" icon="el-icon-download" class="button" plain>导入</el-button>
					<el-button type="warning" size="small" icon="el-icon-upload2" class="button" plain>导出</el-button>
					<el-button type="danger" size="small" icon="el-icon-delete" class="button" plain>批量删除</el-button>
				</el-card>
			</el-col>
			<!-- 数据列表部分 -->
			<el-card class="box-card data">
				<el-table :data="tableData" style="width: 100%" size="small">
					<el-table-column type="selection" width="55"></el-table-column>
					<el-table-column prop="id" label="序号" width="80">
					</el-table-column>
					<el-table-column prop="title" label="通知标题" width="180">
					</el-table-column>
					<el-table-column prop="content" label="通知内容">
					</el-table-column>
					
					<el-table-column label="操作" align="center" width="180px">
						<template slot-scope="">
							<el-button type="primary" size="small" icon="el-icon-edit" class="button" @click="handleUpdate()"  plain>编辑
							</el-button>
							<el-button type="danger" size="small" icon="el-icon-delete" class="button" plain>删除
							</el-button>
						</template>
					</el-table-column>
				</el-table>
				<!-- 分页插件 -->
				<el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange"
					:current-page="currentPage4" :page-size="100" layout="total, prev, pager, next, jumper"
					:total="100">
				</el-pagination>
			</el-card>
		</el-row>
		<!-- 添加弹框插件 -->
		<div class="add-form">
			<el-dialog title="新增通知" :visible.sync="dialogFormVisible">
				<el-row>
					<el-col :span="24">
						<el-form :model="formData" :rules="rules" ref="formData">
							<el-form-item label="标题" prop="college" label-position="right" label-width="100px">
								<el-input v-model="formData.college" autocomplete="off"></el-input>
							</el-form-item>

							<el-form-item label="通知内容" label-position="right" label-width="100px">
								<el-input type="textarea" v-model="formData.profile"></el-input>
							</el-form-item>
						</el-form>
					</el-col>

				</el-row>

				<div slot="footer" class="dialog-footer">
					<el-button @click="dialogFormVisible = false">取 消</el-button>
					<el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
				</div>
			</el-dialog>
		</div>
		<!-- 编辑修改框 -->
		<div class="update-form">
			<el-dialog title="修改信息" :visible.sync="dialogFormVisibleEdit">
				<el-row>
					<el-col :span="24">
						<el-form :model="formData" :rules="rules" ref="formData">
							<el-form-item label="学院名称" prop="college" label-position="right" label-width="100px">
								<el-input v-model="formData.college" autocomplete="off"></el-input>
							</el-form-item>
		
							<el-form-item label="学院地址" prop="address" label-position="right" label-width="100px">
								<el-input v-model="formData.address"></el-input>
							</el-form-item>
							<el-form-item label="联系电话" prop="telephone" label-position="right" label-width="100px">
								<el-input v-model="formData.address"></el-input>
							</el-form-item>
		
							<el-form-item label="学院简介" label-position="right" label-width="100px">
								<el-input type="textarea" v-model="formData.profile"></el-input>
							</el-form-item>
						</el-form>
					</el-col>
		
				</el-row>
		
				<div slot="footer" class="dialog-footer">
					<el-button @click="dialogFormVisible = false">取 消</el-button>
					<el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
				</div>
			</el-dialog>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				tableData: [{
						id: 1,
						title: '关于新学期开学新生报到通知',
						content: '新学期新生报到通知，关于新学期开学新生报到通知关于新学期开学新生报到通知',
						date: '2024-8-23'
					},
					{
						id: 2,
						title: '关于新学期开学新生报到通知',
						content: '新学期新生报到通知，关于新学期开学新生报到通知关于新学期开学新生报到通知',
						date: '2024-8-23'
					}
				],
				dialogFormVisible: false,
				dialogFormVisibleEdit:false,
				/* 表单数据 */
				formData: {
					college: '',
					telephone: '',
					address: '',
					profile: ''
				},
				/*数据校验 */
				rules: {
					college: [{
						required: true,
						message: '请输入学院名称',
						trigger: 'blur'
					}],
					telephone: [{
						required: true,
						message: '请输入联系号码',
						trigger: 'blur'
					}],
					address: [{
						required: true,
						message: '请输入学院地址',
						trigger: 'blur'
					}],

				}
			}
		},
		methods: {
			/* 弹出添加窗口 */
			handleCreate() {
				this.dialogFormVisible = true
			},
			/* 弹出修改窗口 */
			handleUpdate() {
				this.dialogFormVisibleEdit = true
			}
		}

	}
</script>

<style scoped>
	/* 操作框样式 */
	.box-card {
		margin: 5px;
	}

	/* 搜索框样式 */
	.filter-input {
		width: 200px;
		margin-right: 20px;
	}

	/* 按钮样式 */
	.button {
		margin-top: 10px;
	}


	/* 设置表格行间距 */
	/deep/.el-table td,
	.el-table th {
		padding: 5px 0 !important;
	}

	/* 分页插件样式 */
	.el-pagination {
		margin: 10px;
		float: right;
	}
</style>
