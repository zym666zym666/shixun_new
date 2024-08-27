<template>
	<div>
		<el-row>
			<!-- 操作栏 -->
			<el-col :span="24">
				<el-card class="box-card">
					<el-input v-model="searchName" placeholder="请输入课程名称" size="small" class="filter-input"></el-input>

					<el-button type="primary" icon="el-icon-search" size="small" @click="getData()" plain>搜索</el-button>
					<el-button type="primary" icon="el-icon-refresh-right" size="small" @click="resetSearch()">重置</el-button>
					<el-button type="primary" size="small" icon="el-icon-plus" class="button" @click="handleCreate()"
						plain>新增</el-button>

				</el-card>
			</el-col>
			<!-- 数据列表部分 -->
			<el-card class="box-card data">
				<el-table :data="tableData" style="width: 100%" size="small">
					<el-table-column type="selection" width="55"></el-table-column>
					<el-table-column type="index" label="序号" width="80"></el-table-column>
					<el-table-column prop="courseName" label="课程名称" width="180">
					</el-table-column>
					<el-table-column prop="courseTeacher" label="任课教师">
					</el-table-column>
					<el-table-column prop="coursePlace" label="上课地点">
					</el-table-column>
					<el-table-column prop="courseCapacity" label="课程容量">
					</el-table-column>
					<el-table-column label="操作" align="center" width="180px">
						<template slot-scope="scope">
							<el-button type="primary" size="small" icon="el-icon-edit" class="button"
								@click="handleUpdate(scope.row)" plain>编辑
							</el-button>
							<el-button type="danger" size="small" icon="el-icon-delete"
								@click="deleteCourse(scope.row.courseId)" class="button" plain>删除
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
							<el-form-item label="课程名称" label-position="right" label-width="100px">
								<el-input v-model="formData.courseName" autocomplete="off"></el-input>
							</el-form-item>
							<el-form-item label="任课教师" label-position="right" label-width="100px">
								<el-input v-model="formData.courseTeacher"></el-input>
							</el-form-item>
							<el-form-item label="上课地点" label-position="right" label-width="100px">
								<el-input v-model="formData.coursePlace"></el-input>
							</el-form-item>
							<el-form-item label="课程容量" label-position="right" label-width="100px">
								<el-input v-model="formData.courseCapacity"></el-input>
							</el-form-item>
							<el-form-item label="开课时间" label-position="right" label-width="100px">
								<el-input v-model="formData.courseBeginTime"></el-input>
							</el-form-item>
							<el-form-item label="结课时间" label-position="right" label-width="100px">
								<el-input v-model="formData.courseEndTime"></el-input>
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
							<el-form-item label="课程名称" label-position="right" label-width="100px">
								<el-input v-model="formData.courseName" autocomplete="off"></el-input>
							</el-form-item>
							<el-form-item label="教师昵称" label-position="right" label-width="100px">
								<el-input v-model="formData.courseTeacher"></el-input>
							</el-form-item>
							<el-form-item label="教学地点" label-position="right" label-width="100px">
								<el-input v-model="formData.coursePlace"></el-input>
							</el-form-item>
							<el-form-item label="课程容量" label-position="right" label-width="100px">
								<el-input v-model="formData.courseCapacity"></el-input>
							</el-form-item>

						</el-form>
					</el-col>
				</el-row>

				<div slot="footer" class="dialog-footer">
					<el-button @click="dialogFormVisibleEdit = false">取 消</el-button>
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
				name: '',
				date: '',
				tableData: [],
				searchName: '',
				/* 表单数据 */
				formData: {
					courseName: '',
					courseTeacher: '',
					coursePlace: '',
					courseCapacity: '',
					courseBeginTime: '',
					courseEndTime: ''
				},

				/* 分页数据模型 */
				pagination: {
					currentPage: 1, //当前页码
					pageSize: 7, //每页显示的记录数
					total: 0, //总记录数
				},
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
			// 重置按钮
			resetSearch() {
				this.searchName = ''; // 清空搜索条件
				this.handleQuery(); // 重新加载数据
			},
			// 删除课程
			deleteCourse(id) {
				this.$confirm('此操作将永久删除数据, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					//确定删除，发送求到后端控制器
					const urll = `/deleteCourse?id=${id}`;
					this.axios.post(urll).then((res) => {
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

			//查询所有课程信息
			handleQuery() {
				this.axios.get('/allCourse').then((res) => {
					if (res.data.code == 200) {
						this.tableData = res.data.data
					} else {
						this.$message.error("数据加载失败")
					}
				}).catch(() => {
					this.$message.error("网络连接错误,请稍后重试！");
				})
			},
			//搜索课程
			getData() {
				if(!this.searchName)
				{
					this.$message.warning('请输入课程名称');
					return;
				}
				const it = this.tableData.filter(dorm => dorm.courseName=== this.searchName);
				
								if (it.length > 0) {
									this.tableData = it; // 更新表格数据为搜索结果
									this.$message.success('搜索到课程信息');
								} else {
									this.$message.warning('没有找到对应的课程信息，请检查输入是否正确');
								}
			},
			/* 添加课程信息 */
			handleAdd() {
				this.$refs.formData.validate((valid) => {
					if (valid) {
						this.axios.post("/insertCourse", this.formData).then((res) => {
							if (res.data.code == 200) {
								this.$message.success("通知信息已发布");
								//关闭登记窗口
								this.dialogFormVisible = false;
								//重置表单
								this.formData = {}
								// //查询数据
								this.handleQuery();

							} else {
								this.$message.success(res.data.msg);
							}
						}).catch(() => {
							this.$message.error("网络连接错误,请稍后重试！");
						})
					} else {
						this.$message.error("请输入信息");
					}
				})
			},

			/* 修改课程信息 */
			handleEdit() {
				this.$refs.formData.validate((valid) => {
					if (valid) {
						this.axios.post("/updateCourse", this.formData).then((res) => {
							if (res.data.code == 200) {
								this.$message.success("通知信息已发布");
								//关闭登记窗口
								this.dialogFormVisibleEdit = false;
								//重置表单
								this.formData = {}
								// //查询数据
								// this.handleQuery()

							} else {
								this.$message.success(res.data.msg);
							}
						}).catch(() => {
							this.$message.error("网络连接错误,请稍后重试！");
						})
					} else {
						this.$message.error("请输入信息");
					}
				})
			},

			

			/* 分页查询数据 */
			handleCurrentChange(currentPage) {
				this.pagination.currentPage = currentPage
				//重新加载数据
				this.handleQuery()
			},

			/* 条件查询 */

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