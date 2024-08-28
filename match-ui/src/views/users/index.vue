<template>
	<div>
		<el-row>
			<!-- 操作栏 -->
			<el-col :span="24">
				<el-card class="box-card">
					<el-input v-model="searchName" placeholder="请输入课程名称" size="small" class="filter-input"></el-input>

					<el-button type="primary" icon="el-icon-search" size="small" @click="getData()" plain>搜索</el-button>
					<el-button type="primary" icon="el-icon-refresh-right" size="small"
						@click="resetSearch()">重置</el-button>
					<el-button type="primary" size="small" icon="el-icon-plus" class="button" @click="handleCreate()"
						plain>新增</el-button>
					<el-button type="danger" size="small" icon="el-icon-delete" class="button" plain
						@click="handleBatchDelete">批量删除</el-button>
				</el-card>
			</el-col>
			<!-- 数据列表部分 -->
			<el-card class="box-card data">
				<el-table :data="tableData" style="width: 100%" size="small" @selection-change="handleSelectionChange">
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
					<el-table-column prop="courseBeginTime" label="开课时间">
					</el-table-column>
					<el-table-column prop="courseEndTime" label="结课时间">
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
			</el-card>
		</el-row>
		<!-- 添加弹框插件 -->
		<div class="add-form">
			<el-dialog title="新增课程" :visible.sync="dialogFormVisible">
				<el-row>
					<el-col :span="24">
						<el-form :model="formData" :rules="rules" ref="formData">
							<el-form-item label="课程名称" prop="courseName" label-position="right" label-width="100px">
								<el-input v-model="formData.courseName" autocomplete="off"></el-input>
							</el-form-item>
							<el-form-item label="任课教师" prop="courseTeacher" label-position="right" label-width="100px">
								<el-input v-model="formData.courseTeacher"></el-input>
							</el-form-item>
							<el-form-item label="上课地点" prop="coursePlace" label-position="right" label-width="100px">
								<el-input v-model="formData.coursePlace"></el-input>
							</el-form-item>
							<el-form-item label="课程容量" prop="courseCapacity" label-position="right" label-width="100px">
								<el-input v-model="formData.courseCapacity"></el-input>
							</el-form-item>
							<el-form-item label="开课时间" prop="courseBeginTime" label-position="right"
								label-width="100px">
								<el-input v-model="formData.courseBeginTime"></el-input>
							</el-form-item>
							<el-form-item label="结课时间" prop="courseEndTime" label-position="right" label-width="100px">
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
							<el-form-item label="课程名称" prop="courseName" label-position="right" label-width="100px">
								<el-input v-model="formData.courseName" autocomplete="off"></el-input>
							</el-form-item>
							<el-form-item label="教师昵称" prop="courseTeacher" label-position="right" label-width="100px">
								<el-input v-model="formData.courseTeacher"></el-input>
							</el-form-item>
							<el-form-item label="教学地点" prop="coursePlace" label-position="right" label-width="100px">
								<el-input v-model="formData.coursePlace"></el-input>
							</el-form-item>
							<el-form-item label="课程容量" prop="courseCapacity" label-position="right" label-width="100px">
								<el-input v-model="formData.courseCapacity"></el-input>
							</el-form-item>
							<el-form-item label="开始时间" prop="courseBeginTime" label-position="right"
								label-width="100px">
								<el-input v-model="formData.courseBeginTime"></el-input>
							</el-form-item>
							<el-form-item label="结束时间" prop="courseEndTime" label-position="right" label-width="100px">
								<el-input v-model="formData.courseEndTime"></el-input>
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

				tableData: [],
				searchName: [],
				
				/* 表单数据 */
				formData: {
					courseName: '',
					courseTeacher: '',
					coursePlace: '',
					courseCapacity: '',
					courseBeginTime: '',
					courseEndTime: ''
				},
				/*数据校验 */
				//注意：规则名需要和绑定的数据名一致，否则检验失败
				rules: {
					courseName: [{
						required: true,
						message: '请输入课程名称',
						trigger: 'blur'
					}],
					courseTeacher: [{
						required: true,
						message: '请输入任课教师',
						trigger: 'blur'
					}],
					coursePlace: [{
						required: true,
						message: '请输入上课地点',
						trigger: 'blur'
					}],
					courseCapacity: [{
						required: true,
						message: '请输入课程容量(整数)',
						trigger: 'blur'
					}],
					courseBeginTime: [{
						required: true,
						message: '请输入开课时间',
						trigger: 'blur'
					}],
					courseEndTime: [{
						required: true,
						message: '请输入结课时间',
						trigger: 'blur'
					}]
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
			// 重置按钮
			resetSearch() {
				this.searchName = ''; // 清空搜索条件
				this.handleQuery(); // 重新加载数据
			},
			// 删除课程
			deleteCourse(id) {
				this.$confirm('此操作将永久删除该课程, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					//确定删除，发送求到后端控制器
					const urll = `/deleteCourse?id=${id}`;
					this.axios.post(urll).then((res) => {
						if (res.data.code == 200) {
							this.$message.success("课程删除成功")
							//重新加载数据
							this.handleQuery()
						} else {
							this.$message.error("课程删除失败！")
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
						this.$message.error("课程加载失败")
					}
				}).catch(() => {
					this.$message.error("网络连接错误,请稍后重试！");
				})
			},
			//搜索课程
			getData() {
				if (!this.searchName) {
					this.$message.warning('请输入课程名称');
					return;
				}

				// 将搜索词拆分为关键词数组
				const keywords = this.searchName.trim().split(/\s+/); // 假设关键词之间用空格分隔

				// 创建一个正则表达式，包含所有关键词
				const regex = new RegExp(keywords.join('|'), 'i'); // 'i' 表示不区分大小写

				// 使用正则表达式来检查课程名称是否包含所有关键词
				const it = this.tableData.filter(item => regex.test(item.courseName));

				if (it.length > 0) {
					this.tableData = it; // 更新表格数据为搜索结果
					this.$message.success("搜索到相关的课程");
					}
					else {
						this.$message.warning('没有找到包含所有关键词的课程信息，请检查输入是否正确');
					}
				},
				/* 添加课程信息 */
				handleAdd() {
						this.$refs.formData.validate((valid) => {
							if (valid) {
								this.axios.post("/insertCourse", this.formData).then((res) => {
									if (res.data.code == 200) {
										if (res.data.data == 20) {
											this.$message.error("课程已存在,请勿重复添加");
										} else {
											this.$message.success("课程信息已发布");
											//关闭登记窗口
											this.dialogFormVisible = false;
											//重置表单
											this.formData = {};
											// //查询数据
											this.handleQuery();

										}
									} else {
										this.$message.error(res.data.msg);
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
										if (res.data.data == 20) {
											this.$message.error("该课程已存在，修改冲突，请重新修改课程信息");
										} else {
											this.$message.success("课程信息修改成功");
											//关闭登记窗口
											this.dialogFormVisibleEdit = false;
											//重置表单
											this.formData = {}
											// //查询数据
											this.handleQuery();
										}


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

					//批量删除课程
					handleBatchDelete() {
						console.log("1111", this.tableData);
						const selectedRows = this.tableData.filter(item => item.selected);
						const selectedIds = selectedRows.map(row => row.courseId);
						if (selectedIds.length === 0) {
							this.$message.warning('请选择至少一门课程进行删除');
							return;
						}
						this.$confirm('此操作将永久删除选中课程, 是否继续?', '提示', {
							confirmButtonText: '确定',
							cancelButtonText: '取消',
							type: 'warning'
						}).then(() => {

							console.log(selectedIds);
							const it = `/DeleteCourses?ids=${selectedIds}`;
							this.axios.post(it).then((res) => {
								if (res.data.code == 200) {
									this.$message.success("课程批量删除成功");
									this.handleQuery(); // 重新加载数据
								} else {
									this.$message.error("课程批量删除失败");
								}
							}).catch(() => {
								this.$message.error("网络连接错误，请稍后重试！");
							});
						}).catch(() => {
							this.$message({
								type: 'info',
								message: '已取消批量删除'
							});
						});
					},
					handleSelectionChange(val) {
						this.tableData.forEach((row) => {
							row.selected = val.includes(row);
						});
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



	.el-switch {
		margin-top: 10px;
		margin-left: 10px;
	}
</style>