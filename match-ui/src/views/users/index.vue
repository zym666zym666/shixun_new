<template>
	<div>
		<el-row>
			<!-- 操作栏 -->
			<el-col :span="24">
				<el-card class="box-card">
					<el-input v-model="username" placeholder="请输入用户名称" size="small" class="filter-input"></el-input>
					<el-select v-model="role" placeholder="请选择用户角色" class="filter-input" size="small">
						<el-option label="管理员" value="0"></el-option>
						<el-option label="教师" value="1"></el-option>
						<el-option label="学生" value="2"></el-option>
					</el-select>
					<el-button type="primary" icon="el-icon-search" size="small" @click="getData()" plain>搜索</el-button>
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
					<el-table-column type="index" label="序号" width="80">
					</el-table-column>
					<el-table-column prop="username" label="用户名称" width="180">
					</el-table-column>
					<el-table-column prop="telephone" label="联系电话">
					</el-table-column>
					<el-table-column prop="nickname" label="昵称">
					</el-table-column>
					<el-table-column prop="role" label="角色">
						<template slot-scope="scope">
							<span v-if="scope.row.role==0">管理员</span>
							<span v-if="scope.row.role==1">教师</span>
							<span v-if="scope.row.role==2">学生</span>
						</template>
					</el-table-column>
					<el-table-column prop="state" label="状态">
						<template slot-scope="scope">
							<el-tag type="success" v-if="scope.row.state==1">启用</el-tag>
							<el-tag type="success" v-if="scope.row.state==0">禁用</el-tag>
						</template>
					</el-table-column>
					<el-table-column prop="createDate" label="创建日期">
					</el-table-column>
					<el-table-column label="操作" align="center" width="180px">
						<template slot-scope="scope">
							<el-button type="primary" size="small" icon="el-icon-edit" class="button"
								@click="handleUpdate(scope.row)" plain>编辑
							</el-button>
							<el-button type="danger" size="small" icon="el-icon-delete"
								@click="deleteUser(scope.row.id)" class="button" plain>删除
							</el-button>
						</template>
					</el-table-column>
				</el-table>
				<!-- 分页插件 -->
				<el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange"
					:current-page="pagination.currentPage" :page-size="pagination.pageSize"
					layout="total, prev, pager, next, jumper" :total="pagination.total">
				</el-pagination>
			</el-card>
		</el-row>
		<!-- 添加弹框插件 -->
		<div class="add-form">
			<el-dialog title="新增用户" :visible.sync="dialogFormVisible">
				<el-row>
					<el-col :span="24">
						<el-form :model="formData" :rules="rules" ref="formData">
							<el-form-item label="用户名称" prop="username" label-position="right" label-width="100px">
								<el-input v-model="formData.username" autocomplete="off"></el-input>
							</el-form-item>

							<el-form-item label="用户昵称" prop="nickname" label-position="right" label-width="100px">
								<el-input v-model="formData.nickname"></el-input>
							</el-form-item>
							<el-form-item label="联系电话" prop="telephone" label-position="right" label-width="100px">
								<el-input v-model="formData.telephone"></el-input>
							</el-form-item>
							<el-form-item label="用户角色" label-position="right" label-width="100px" prop="role">
								<el-select v-model="formData.role" placeholder="请选择用户角色" style="width: 100%;">
									<el-option label="管理员" value="0"></el-option>
									<el-option label="教师" value="1"></el-option>
									<el-option label="学生" value="2"></el-option>
								</el-select>
							</el-form-item>
							<el-form-item label="用户状态" label-position="right" label-width="100px">
								<el-switch style="display: block" v-model="formData.state" active-color="#13ce66"
									inactive-color="#ff4949" active-text="启用" inactive-text="禁用" :active-value="1"
									:inactive-value="0">
								</el-switch>
							</el-form-item>
						</el-form>
					</el-col>
				</el-row>

				<div slot="footer" class="dialog-footer">
					<el-button @click="dialogFormVisible = false">取 消</el-button>
					<el-button type="primary" @click="handleAdd()">确 定</el-button>
				</div>
			</el-dialog>
		</div>
		<!-- 编辑修改框 -->
		<div class="update-form">
			<el-dialog title="修改信息" :visible.sync="dialogFormVisibleEdit">
				<el-row>
					<el-col :span="24">
						<el-form :model="formData" :rules="rules" ref="formData">
							<el-form-item label="用户名称" prop="username" label-position="right" label-width="100px">
								<el-input v-model="formData.username" autocomplete="off"></el-input>
							</el-form-item>

							<el-form-item label="用户昵称" prop="nickname" label-position="right" label-width="100px">
								<el-input v-model="formData.nickname"></el-input>
							</el-form-item>
							<el-form-item label="联系电话" prop="telephone" label-position="right" label-width="100px">
								<el-input v-model="formData.telephone"></el-input>
							</el-form-item>
							<el-form-item label="用户角色" label-position="right" label-width="100px" prop="role">
								<el-select v-model="formData.role" placeholder="请选择用户角色" style="width: 100%;">
									<el-option label="管理员" :value="0"></el-option>
									<el-option label="教师" :value="1"></el-option>
									<el-option label="学生" :value="2"></el-option>
								</el-select>
							</el-form-item>
							<el-form-item label="用户状态" label-position="right" label-width="100px">
								<el-switch style="display: block" v-model="formData.state" active-color="#13ce66"
									inactive-color="#ff4949" active-text="启用" inactive-text="禁用" :active-value="1"
									:inactive-value="0">
								</el-switch>
							</el-form-item>
						</el-form>
					</el-col>
				</el-row>

				<div slot="footer" class="dialog-footer">
					<el-button @click="dialogFormVisible = false">取 消</el-button>
					<el-button type="primary" @click="handleEdit()">确 定</el-button>
				</div>
			</el-dialog>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dialogFormVisible: false,
				dialogFormVisibleEdit: false,
				name:'',
				date:'',
				tableData: [],
				/* 表单数据 */
				formData: {
					username: '',
					telephone: '',
					nickname: '',
					role: '',
					state: '',
				},

				/* 分页数据模型 */
				pagination: {
					currentPage: 1, //当前页码
					pageSize: 7, //每页显示的记录数
					total: 0, //总记录数
				},
				/*数据校验 */
				rules: {
					username: [{
						required: true,
						message: '请输入用户名称',
						trigger: 'blur'
					}],
					nickname: [{
						required: true,
						message: '请输入用户昵称',
						trigger: 'blur'
					}],
					telephone: [{
						required: true,
						message: '请输入联系号码',
						trigger: 'blur'
					}],
					role: [{
						required: true,
						message: '请选择角色',
						trigger: 'change'
					}],
				}
			}
		},
		mounted() {
			this.handleQuery()
		},
		methods: {
			/* 弹出添加窗口 */
			handleCreate() {
				this.dialogFormVisible = true
			},

			/* 弹出修改窗口 */
			handleUpdate(row) {
				this.dialogFormVisibleEdit = true,
					//将数据填充到formData
					this.formData = row
			},

			/* 查询所有用户信息 */
			handleQuery() {
				this.axios.get("/users?currentPage=" + this.pagination.currentPage + "&pageSize=" + this.pagination
						.pageSize)
					.then((res) => {
						if (res.data.code == 200) {
							//将数据设置到表格中
							this.tableData = res.data.data.list
							//设置总记录数
							this.pagination.total = res.data.data.total
						}
					})
			},

			/* 添加用户信息 */
			handleAdd() {
				this.axios.post("/users", this.formData).then((res) => {
					if (res.data.code == 200) {
						this.$message.success("用户信息添加成功")
						//关闭窗口
						this.dialogFormVisible = false
						//重新加载数据
						this.handleQuery()
						//重置表单数据
						this.formData = {}
					} else {
						this.$message.error("用户信息添加失败,稍后重试")
					}
				}).catch(() => {
					this.$message.waring("网络连接错误")
				})
			},

			/* 修改用户信息 */
			handleEdit() {
				this.axios.put("/users", this.formData).then((res) => {
					if (res.data.code == 200) {
						this.$message.success("用户信息修改成功")
						//关闭窗口
						this.dialogFormVisibleEdit = false
						//重新加载数据
						this.handleQuery()
						//重置表单数据
						this.formData = {}
					} else {
						this.$message.error("用户信息修改失败,稍后重试")
					}
				}).catch(() => {
					this.$message.waring("网络连接错误")
				})
			},

			/* 删除用户信息 */
			deleteUser(id) {
				this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					//确定删除，发送求到后端控制器
					this.axios.delete("/users/" + id).then((res) => {
						if (res.data.code == 200) {
							this.$message.success("数据删除成功")
							//重新加载数据
							this.handleQuery()
						} else {
							this.$message.error("数据删除失败！")
						}
					})
				}).catch(() => {
					this.$message({
						type: 'info',
						message: '已取消删除'
					});
				});
			},
			/* 分页查询数据 */
			handleCurrentChange(currentPage) {
				this.pagination.currentPage = currentPage
				//重新加载数据
				this.handleQuery()
			},

			/* 条件查询 */
			getData() {
				this.axios.get("/query?currentPage=" + this.pagination.currentPage + "&pageSize=" + this.pagination
						.pageSize+"&name="+this.name+"&date="+this.date)
					.then((res) => {
						if (res.data.code == 200) {
							//将数据设置到表格中
							this.tableData = res.data.data.list
							//设置总记录数
							this.pagination.total = res.data.data.total
						}
					})
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

	.el-switch {
		margin-top: 10px;
		margin-left: 10px;
	}
</style>
