<template>
	<div>
		<el-row>
			<!-- 操作栏 -->
			<el-col :span="24">
				<el-card class="box-card">
					<el-input v-model="inputBuildingId" placeholder="请输入宿舍号" size="small"
						class="filter-input"></el-input>
					<el-button type="primary" icon="el-icon-search" size="small" plain
						@click="selectDorm(inputBuildingId)">搜索</el-button>
					<el-button type="primary" icon="el-icon-refresh-right" size="small"
						@click="resetSearch()">重置</el-button>
					<el-button type="primary" size="small" icon="el-icon-plus" class="button" @click="handleCreate()"
						plain>新增</el-button>
					<el-button type="danger" size="small" icon="el-icon-delete" class="button" plain @click="handleBatchDelete()">批量删除</el-button>
				</el-card>

			</el-col>
			<!-- 数据列表部分 -->
			<el-card class="box-card data">
				<el-table :data="tableData" style="width: 100%" size="small" @selection-change="handleSelectionChange">
					<el-table-column type="selection" width="55"></el-table-column>

					<el-table-column prop="building_id" label="宿舍号" width="400%"></el-table-column>
					<!-- 列标题为"最大容量"，内容固定为4 -->
					<el-table-column label="最大容量" width="400%">
						<template>
							4 <!-- 这里固定显示为4 -->
						</template>
					</el-table-column>
					<el-table-column prop="occupied_count" label="已入住人数"></el-table-column>

					<el-table-column label="操作" align="center" width="200%">
						<template slot-scope="scope">
							<el-button type="danger" size="small" icon="el-icon-delete" class="button" plain
								@click="handleDelete(scope.row.building_id)">删除
							</el-button>
						</template>
					</el-table-column>
				</el-table>
			</el-card>
		</el-row>
		<!-- 添加弹框插件 -->
		<div class="add-form">
			<el-dialog title="新增宿舍" :visible.sync="dialogFormVisible">
				<el-row>
					<el-col :span="24">
						<el-form :model="formData" :rules="rules" ref="formData">
							<el-form-item label="宿舍号" prop="buildingId" label-position="right" label-width="100px">
								<el-input v-model="formData.buildingId" autocomplete="off"></el-input>
							</el-form-item>
						</el-form>
					</el-col>

				</el-row>

				<div slot="footer" class="dialog-footer">
					<el-button @click="dialogFormVisible = false">取 消</el-button>
					<el-button type="primary" @click="handleAdd(),dialogFormVisible = false">确 定</el-button>
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
					buildingId: '',
					content: '',

				},

				inputBuildingId: "",
			}
		},
		mounted() {
			//页面加载完后调用查询数据的方法
			this.handleQuery()
		},
		methods: {
			/* 弹出添加窗口 */
			handleCreate() {
				this.dialogFormVisible = true
			},

			/**
			 * 新增宿舍
			 */
			handleAdd() {
				if (!this.formData.buildingId) {
					this.$message.error("请输入宿舍号");
					return;
				}
				const url = `/addDorm?buildingId=${this.formData.buildingId}`
				// 使用Axios发送POST请求到后端API
				this.axios.post(url).then(response => {
					if (response.data.code == 200) {
						// 如果请求成功，关闭对话框并清空表单
						this.dialogFormVisible = false;
						this.formData.buildingId = ''; // 重置表单数据
						// 重新查询数据以更新表格
						this.handleQuery();
						this.$message.success("宿舍信息已添加");
					} else {
						// 处理错误情况
						this.$message.error(response.data.msg || "添加失败，请稍后重试");
					}
				}).catch(error => {
					// 网络或其他错误处理
					this.$message.error("网络连接错误或服务器故障，请稍后重试！");
					console.error("添加宿舍失败：", error); // 在控制台输出错误详情
				});
			},


			//查询数据
			handleQuery() {
				this.axios.get('/getAllDormId').then((res) => {
					if (res.data.code == 200) {
						this.tableData = res.data.data

						console.log(this.tableData)
					} else {
						this.$message.error("数据加载失败")
					}
				}).catch(() => {
					this.$message.error("网络连接错误,请稍后重试！");
				})
			},

			//删除宿舍
			handleDelete(buildingId) {
				this.$confirm('此操作将永久删除宿舍, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					//确定删除，发送求到后端控制器
					const urll = `/deleteDorm?buildingId=${buildingId}`;
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

			//关键字查询
			selectDorm(keyword) {
				if (!keyword) {
					this.$message.warning('请输入搜索关键字');
					return;
				}

				// 过滤宿舍列表，找到宿舍号包含关键字的数据
				const filteredDorms = this.tableData.filter(dorm => dorm.building_id.toString().includes(keyword));

				if (filteredDorms.length > 0) {
					this.tableData = filteredDorms; // 更新表格数据为搜索结果
					this.$message.success('搜索到宿舍信息');
				} else {
					this.$message.warning('没有找到包含关键字的宿舍信息');
				}
			},

			// 重置搜索输入框和表格数据
			resetSearch() {
				this.inputBuildingId = ''; // 重置搜索输入框
				this.handleQuery(); // 重新加载原始宿舍数据
				this.$message.info('搜索已重置');
			},

			//批量删除宿舍
			handleBatchDelete() {
				const selectedRows = this.tableData.filter(item => item.selected);
				const selectedIds = selectedRows.map(row => row.building_id);

				if (selectedIds.length === 0) {
					this.$message.warning('请选择至少一个宿舍进行删除');
					return;
				}
				this.$confirm('此操作将永久删除选中宿舍, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					console.log(selectedIds);
					const it = `/DeleteDorms?buildingId=${selectedIds}`;
					this.axios.post(it).then((res) => {
						if (res.data.code == 200) {
							this.$message.success("宿舍批量删除成功");
							this.handleQuery(); // 重新加载数据
						} else {
							this.$message.error("宿舍批量删除失败");
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
				console.log("Selected Rows: ", val); // 打印选中的行数据
				this.tableData.forEach(row => {
					// 设置一个默认值，表示这行未被选中
					row.selected = false;
				});
				// 然后遍历所有选中的行
				val.forEach(selectedRow => {
					// 根据唯一标识符（例如 building_id）来设置 selected 属性
					this.tableData.forEach(row => {
						if (row.building_id === selectedRow.building_id) {
							row.selected = true;
						}
					});
				});
			},
		}

	}
</script>

<style scoped>
	/* 操作框样式 */
	.box-card {
		margin: 5px;
		width: 100%;
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