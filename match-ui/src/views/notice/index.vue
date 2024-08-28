<template>
	<div>
		<el-row>
			<!-- 操作栏 -->
			<el-col :span="24">
				<el-card class="box-card">
					<el-input v-model="input" placeholder="请输入通知标题" size="small" class="filter-input"></el-input>
					<el-button type="primary" icon="el-icon-search" size="small" @click="search()" plain>搜索</el-button>
					<el-button type="primary" icon="el-icon-refresh-right" size="small"
						@click="clear">重置</el-button><br />

					<el-button type="primary" size="small" icon="el-icon-plus" class="button" @click="handleCreate()"
						plain>新增</el-button>
					<el-button type="danger" size="small" icon="el-icon-delete" class="button"
						@click="handleBatchDelete()" plain>批量删除</el-button>

				</el-card>
			</el-col>
			<!-- 数据列表部分 -->
			<el-card class="box-card data">
				<el-table :data="tableData" style="width: 100%" size="small" @selection-change="handleSelectionChange">
					<el-table-column type="selection" width="55"></el-table-column>
					<el-table-column prop="id" label="序号" width="80">
					</el-table-column>
					<el-table-column prop="title" label="通知标题" width="180">
					</el-table-column>
					<el-table-column prop="content" label="通知内容">
					</el-table-column>

					<el-table-column label="操作" align="center" width="180px">
						<template slot-scope="scope">
							<el-button type="primary" size="small" icon="el-icon-edit" class="button"
								@click="handleUpdate(scope.row)" plain>编辑
							</el-button>
							<el-button type="danger" size="small" icon="el-icon-delete" class="button" plain
								@click="handleDelete(scope.row.id)">删除
							</el-button>
						</template>
					</el-table-column>
				</el-table>
				<el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange"
					:current-page="pagination.currentPage" :page-size="pagination.pageSize" :total="pagination.total"
					layout="total, prev, pager, next, jumper">
				</el-pagination>
			</el-card>

		</el-row>
		<!-- 添加弹框插件 -->
		<div class="add-form">
			<el-dialog title="新增通知" :visible.sync="dialogFormVisible">
				<el-row>
					<el-col :span="24">
						<el-form :model="formData" :rules="rules" ref="formData">
							<el-form-item label="标题" prop="title" label-position="right" label-width="100px">
								<el-input v-model="formData.title" autocomplete="off"></el-input>
							</el-form-item>

							<el-form-item label="通知内容" label-position="right" label-width="100px" prop="content">
								<el-input type="textarea" v-model="formData.content"></el-input>
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
			<el-dialog title="修改信息" :visible.sync="dialogFormVisibleEdit" >
				<el-row>
					<el-col :span="24">
						<el-form :model="formData" :rules="rules" ref="formData">
							<el-form-item label="标题" prop="college" label-position="right" label-width="100px">
								<el-input v-model="formData.title" autocomplete="off"></el-input>
							</el-form-item>

							<el-form-item label="通知内容" label-position="right" label-width="100px">
								<el-input type="textarea" v-model="formData.content"></el-input>
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
				tableData: [],
				dialogFormVisible: false,
				dialogFormVisibleEdit: false,
				/* 表单数据 */
				formData: {
					title: '',
					content: '',

				},
				selectedIds: [],
				selectedRows: [],
				//分页数据模型
				pagination: {
					currentPage: 1,
					pageSize: 5,
					total: "",
				},
				input: "",
				/*数据校验 */
				rules: {
					title: [{
						required: true,
						message: '请输入通知标题',
						trigger: 'blur'
					}],
					content: [{
						required: true,
						message: '请输入通知内容',
						trigger: 'blur'
					}]


				}
			}
		},
		mounted() {
			//页面加载完后调用查询数据的方法
			this.getpage();
		},
		methods: {
			//批量删除
			handleBatchDelete() {
				const selectedRows = this.tableData.filter(item => item.selected);
				const selectedIds = selectedRows.map(row => row.id);
				if (selectedIds.length === 0) {
					this.$message.warning('请选择至少一个条目进行删除');
					return;
				}
				console.log(selectedIds);
				this.$confirm('此操作将永久删除选中数据, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					const it = `/Delete?ids=${selectedIds}`;
					this.axios.post(it).then((res) => {
						if (res.data.code == 200) {
							this.$message.success("数据批量删除成功");
							this.pagination.total = this.pagination.total - selectedRows.length;

							this.getpage() // 重新加载数据
						} else {
							this.$message.error("数据批量删除失败");
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
			//分页查询方法
			handleCurrentChange(currentPage) {
				this.pagination.currentPage = currentPage;
				this.getpage();
			},
			handleSizeChange(newSize) {
				this.pagination.pageSize = newSize;
				this.getpage();
			},
			getpage() {
				const url = `/noticeList`;
				this.axios.post(url, {
					pageSize: this.pagination.pageSize,
					pageNum: this.pagination.currentPage,
					param: {
						title: this.input,
					}
				}).then((res) => {
					if (res.data.code == 200) {
						this.tableData = res.data.data.records; // 更新数据
						this.pagination.total = res.data.data.total; // 更新总条目数
						// this.pagination.currentPage = res.data.data.pageNum; // 更新当前页码
						// this.pagination.pageSize = res.data.data.pageSize; // 更新每页显示条数
					}
				}).catch((error) => {
					console.error("请求失败", error);
					this.$message.error("数据加载失败");
				});
			},
			/* 弹出添加窗口 */
			handleCreate() {
				this.dialogFormVisible = true;
				this.formData = {
					title: '',
					content: ''
				};
			},
			/* 弹出修改窗口 */
			handleUpdate(row) {
				this.dialogFormVisibleEdit = true
				this.formData = {
					...row
				};
			},
			/**
			 * 发布通知
			 */
			handleAdd() {
				this.$refs.formData.validate((valid) => {
					if (valid) {
						this.axios.post("/notice", this.formData).then((res) => {
							if (res.data.code == 200) {
								this.$message.success("通知信息已发布");
								//关闭登记窗口
								this.dialogFormVisible = false;
								//重置表单
								this.formData = {
									title: '',
									content: ''
								};
								this.pagination.total = this.pagination.total + 1;
								//查询数据
								//this.handleQuery()
								this.$nextTick(() => {
									this.getpage();
								});
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

			/* 修改认领信息 */
			handleEdit() {
				this.axios.put("/notice", this.formData).then((res) => {
					if (res.data.code == 200) {
						this.$message.success("认领信息修改成功")
						//关闭窗口
						this.dialogFormVisibleEdit = false
						//重新加载数据
						this.getpage();
						//重置表单数据
						this.formData = {
							title: '',
							content: ''
						};
					} else {
						this.$message.error("信息修改失败,稍后重试")
					}
				}).catch(() => {
					this.$message.waring("网络连接错误")
				})
			},
			handlePageChange(currentPage) {
				this.pagination.currentPage = currentPage;
				const start = (currentPage - 1) * this.pagination.pageSize;
				const end = start + this.pagination.pageSize;
				this.tableData = this.tableData.slice(start, end);
			},
			/* 删除通知信息 */
			handleDelete(id) {
				this.$confirm('此操作将永久删除数据, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					//确定删除，发送求到后端控制器
					this.axios.delete("/notice/" + id).then((res) => {
						if (res.data.code == 200) {
							this.$message.success("数据删除成功")
							this.pagination.total = this.pagination.total - 1;
							//重新加载数据
							this.getpage();
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
			search() {
				const url = `/noticeList`;
				this.axios.post(url, {
					pageSize: this.pagination.pageSize,
					pageNum: 1,
					param: {
						title: this.input,
					}
				}).then((res) => { // 缺少了括号
					if (res.data.code == 200) {
						this.$message.success("搜索请求成功");
						this.pagination.currentPage = 1;
						this.getpage();
					}
				}).catch(() => {
					this.$message.error("搜索请求失败");
				});
			},
			clear() {
				this.input = "";
				this.getpage();
			},

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