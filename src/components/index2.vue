 <template>
     <!-- @author：卢芮 -->
     <div>
         <!-- @tab-click="handleClick" -->
         <el-tabs v-model="activeName">
             <el-tab-pane label="未完成" name="first">
                 <el-button type="primary" @click="formDialogShowMethod">点击添加事件</el-button>
                 <el-button type="success" @click="sortByTask_level">点击根据任务等级排序</el-button>
                 <el-button type="info" @click="sortByCompletion_status">点击根据任务完成度排序</el-button>
                 <el-col :span="12">
                     <div class="sub-title">请输入要查找的事件</div>
                     <!-- :fetch-suggestions="querySearch" -->
                     <!-- <el-autocomplete class="inline-input" v-model="input_search" 
                        placeholder="请输入内容" :trigger-on-focus="false"></el-autocomplete> -->
                     <el-input v-model="input_search" placeholder="请输入关键字"></el-input>
                 </el-col>

                 <!-- 任务待完成表格 -->
                 <el-table :data="searchFromKeyWords(input_search)" style="width: 100%">
                     <el-table-column label="序号" width="180">
                         <template slot-scope="scope">
                             <span>{{ scope.$index + 1 }}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="任务名称" width="180">
                         <template slot-scope="scope">
                             <span>{{scope.row.task_name}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="开始时间" width="180">
                         <template slot-scope="scope">
                             <span>{{scope.row.start_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="结束时间">
                         <template slot-scope="scope">
                             <span>{{scope.row.end_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="任务等级">
                         <template slot-scope="scope">
                             <span>{{scope.row.task_level | jugementTaskLevel }}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="完成情况">
                         <template slot-scope="scope">
                             <span>{{scope.row.completion_status }}</span>
                         </template>
                     </el-table-column>
                     <el-table-column prop="options" label="具体操作">
                         <template slot-scope="scope">
                             <el-button type="text" size="small"
                                 @click="detailDialogShowMethod(scope.row.task_details)">
                                 查看详情
                             </el-button>
                             <el-button type="text" size="small"
                                 @click="detailDialogEditMethod(scope.$index,scope.row)">
                                 编辑</el-button>
                             <el-button type="text" size="small" @click="finishTask(scope.$index,scope.row)">完成
                             </el-button>
                             <el-button type="text" size="small" @click="deleteTask(scope.$index,scope.row)">删除
                             </el-button>
                         </template>
                     </el-table-column>
                 </el-table>

             </el-tab-pane>

             <!-- 任务已经完成表格 -->
             <el-tab-pane label="已完成" name="second">已完成
                 <el-table :data="finishTaskList" style="width: 100%">
                     <el-table-column label="序号" width="180">
                         <template slot-scope="scope">
                             <span>{{ scope.$index + 1 }}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="任务名称" width="180">
                         <template slot-scope="scope">
                             <span>{{scope.row.task_name}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="开始时间" width="180">
                         <template slot-scope="scope">
                             <span>{{scope.row.start_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="结束时间">
                         <template slot-scope="scope">
                             <span>{{scope.row.end_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="完成时间">
                         <template slot-scope="scope">
                             <span>{{scope.row.finished_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <!-- <el-table-column label="任务等级">
                        <template slot-scope="scope">
                            <span>{{scope.row.task_level | jugementTaskLevel }}</span>
                        </template>
                    </el-table-column> -->
                     <!-- <el-table-column label="完成情况">
                        <template slot-scope="scope">
                            <span>{{scope.row.completion_status }}</span>
                        </template>
                    </el-table-column> -->
                     <el-table-column prop="options" label="具体操作">
                         <template slot-scope="scope">
                             <el-button type="text" size="small"
                                 @click="restoreFromFinishTaskList(scope.$index,scope.row)">
                                 还未完成?点击还原
                             </el-button>

                             <el-button type="text" size="small"
                                 @click="deletedFromFinishTaskList(scope.$index,scope.row)">删除</el-button>
                         </template>
                     </el-table-column>
                 </el-table>
             </el-tab-pane>

             <!-- 已删除的列表 -->
             <el-tab-pane label="已删除" name="third">已删除
                 <el-table :data="deletedTaskList" style="width: 100%">
                     <el-table-column label="序号" width="180">
                         <template slot-scope="scope">
                             <span>{{ scope.$index + 1 }}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="任务名称" width="180">
                         <template slot-scope="scope">
                             <span>{{scope.row.task_name}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="开始时间" width="180">
                         <template slot-scope="scope">
                             <span>{{scope.row.start_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="结束时间">
                         <template slot-scope="scope">
                             <span>{{scope.row.end_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="删除时间">
                         <template slot-scope="scope">
                             <span>{{scope.row.deleted_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <!-- <el-table-column label="任务等级">
                        <template slot-scope="scope">
                            <span>{{scope.row.task_level | jugementTaskLevel }}</span>
                        </template>
                    </el-table-column> -->
                     <el-table-column label="完成情况">
                         <template slot-scope="scope">
                             <span>{{scope.row.completion_status }}</span>
                         </template>
                     </el-table-column>
                     <el-table-column prop="options" label="具体操作">
                         <template slot-scope="scope">
                             <el-button type="text" size="small"
                                 @click="reductionFromDeletedToUnfinish(scope.$index,scope.row)">
                                 还原
                             </el-button>

                             <el-button type="text" size="small"
                                 @click="deletedFromDeletedTaskList(scope.$index,scope.row)">彻底删除</el-button>
                         </template>
                     </el-table-column>
                 </el-table>
             </el-tab-pane>

             <!-- 帮助 -->
             <el-tab-pane label="帮助" name="fourth">这里是帮助文档
                 <div>
                     <el-link type="primary" href="https://github.com/bczzlr">点击访问我的GitHub主页</el-link>
                 </div>
             </el-tab-pane>
         </el-tabs>

         <!--详细信息弹出框-->
         <el-dialog title="详细信息" :visible.sync="dialogDetailsVisible" width="30%" :before-close="handleClose">
             <span>{{detailDialogText}}</span>

         </el-dialog>

         <!-- 添加任务弹出框 -->
         <el-dialog title="添加任务" :visible.sync="dialogFormVisible">
             <el-form :model="addForm">
                 <el-form-item label="任务名称" :label-width="formLabelWidth">
                     <el-input v-model="addForm.task_name" autocomplete="off"></el-input>
                 </el-form-item>

                 <el-form-item label="任务详情" :label-width="formLabelWidth">
                     <el-input v-model="addForm.task_details" autocomplete="off"></el-input>
                 </el-form-item>

                 <!-- <div class="block" align="left">
                    <span class="demonstration">请选择开始日期:</span>
                    <el-date-picker v-model="addForm.start_time" align="right" type="date" placeholder="选择日期"
                        :picker-options="pickerOptions_start">
                    </el-date-picker>
                </div> -->
                 <el-form-item label="任务等级:" :label-width="formLabelWidth">
                     <div align="left">
                         <el-select v-model="addForm.task_level " placeholder="请选择任务等级">
                             <el-option label="一般任务" value=0></el-option>
                             <el-option label="较为重要" value=1></el-option>
                             <el-option label="急需完成" value=2></el-option>
                         </el-select>
                     </div>
                 </el-form-item>


                 <div align="left">
                     <div>
                         <span class="demonstration">请选择开始日期:</span>
                         <div class="demonstration"></div>
                         <el-date-picker v-model="addForm.start_time" type="date" placeholder="选择日期"
                             format="yyyy 年 MM 月 dd 日">
                         </el-date-picker>
                     </div>

                     <!-- <div class="block" align="left">
                    <span class="demonstration">请选择结束日期:</span>
                    <el-date-picker v-model="addForm.end_time" align="right" type="date" placeholder="选择日期"
                        :picker-options="pickerOptions_end">
                    </el-date-picker>
                </div> -->
                     <div style="margin-top:20px;">
                         <span class="demonstration">请选择结束日期:</span>
                         <div class="demonstration"></div>
                         <el-date-picker v-model="addForm.end_time" type="date" placeholder="选择日期"
                             format="yyyy 年 MM 月 dd 日">
                         </el-date-picker>
                     </div>
                 </div>
                 <div class="block" style="margin-top:20px;">
                     <span class="demonstration">任务完成情况</span>
                     <el-slider v-model="addForm.completion_status"></el-slider>
                 </div>

                 <!-- <el-progress :percentage="addForm.completion_status"></el-progress> -->
                 <!-- <div class="block">
                     <span class="demonstration">任务完成情况</span>
                     <el-slider v-model="addForm.completion_status"></el-slider>
                 </div> -->

             </el-form>
             <div slot="footer" class="dialog-footer">
                 <el-button @click="cancelTask">取 消</el-button>
                 <el-button type="primary" @click="addTask">确 定</el-button>
             </div>
         </el-dialog>


     </div>
 </template>



 <script>
     export default {
         data() {

             return {
                 activeName: 'second',
                 mag: 'haha',
                 //是否是插入添加
                 isAdd: false,
                 // 任务详情对话框具体内容
                 detailDialogText: '',
                 // 任务详情对话框是否可显示
                 dialogDetailsVisible: false,
                 //添加任务对话框是否显示
                 dialogFormVisible: false,
                 //输入搜索框
                 input_search: '',
                 //添加事件form对象
                 addForm: {
                     task_name: "",
                     task_details: "",
                     start_time: null,
                     end_time: null,
                     deleted_time: null,
                     finished_time: null,
                     task_level: 0,
                     completion_status: 0
                 },
                 //未完成事件list
                 unfinishTaskList: [{
                         task_name: "跑步",
                         task_details: "跑一万米",
                         start_time: new Date(),
                         end_time: new Date(),
                         deleted_time: null,
                         finished_time: null,
                         task_level: 0,
                         completion_status: 0

                     },
                     {
                         task_name: "写作业",
                         task_details: "写完所有作业",
                         start_time: new Date(),
                         end_time: new Date(),
                         deleted_time: null,
                         finished_time: null,
                         task_level: 1,
                         completion_status: 0

                     }
                 ],

                 //当前编辑事件的索引
                 editIndex: 0,
                 //完成事件list
                 finishTaskList: [{
                     task_name: "大数据实验报告",
                     task_details: "大数据实验报告四份写完提交",
                     start_time: new Date(),
                     end_time: new Date(),
                     deleted_time: null,
                     finished_time: new Date(),
                     task_level: 2,
                     completion_status: 100
                 }],
                 //删除事件list
                 deletedTaskList: [{
                     task_name: "吃饭",
                     task_details: "吃好多好吃的",
                     start_time: new Date(),
                     end_time: new Date(),
                     deleted_time: new Date(),
                     finished_time: null,
                     task_level: 1,
                     completion_status: 79
                 }],

                 //时间选择器数据开始
                 pickerOptions_start: {
                     disabledDate(time) {
                         return time.getTime() > Date.now();
                     },
                     shortcuts: [{
                         text: '今天',
                         onClick(picker) {
                             picker.$emit('pick', new Date());
                         }
                     }, {
                         text: '昨天',
                         onClick(picker) {
                             const date = new Date();
                             date.setTime(date.getTime() - 3600 * 1000 * 24);
                             picker.$emit('pick', date);
                         }
                     }, {
                         text: '一周前',
                         onClick(picker) {
                             const date = new Date();
                             date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
                             picker.$emit('pick', date);
                         }
                     }]
                 },
                 pickerOptions_end: {
                     disabledDate(time) {
                         return time.getTime() > Date.now();
                     },
                     shortcuts: [{
                         text: '今天',
                         onClick(picker) {
                             picker.$emit('pick', new Date());
                         }
                     }, {
                         text: '昨天',
                         onClick(picker) {
                             const date = new Date();
                             date.setTime(date.getTime() - 3600 * 1000 * 24);
                             picker.$emit('pick', date);
                         }
                     }, {
                         text: '一周前',
                         onClick(picker) {
                             const date = new Date();
                             date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
                             picker.$emit('pick', date);
                         }
                     }]
                 },
                 value1: '',
                 value2: '',
                 //时间选择器数据结束
             };
         },
         //方法：
         //注：在注释中所有List我形容成了列表不太准确，应为数组或者集合更加准确 @author：卢芮
         methods: {
             // handleClick(tab, event) {
             //     console.log(tab, event);
             // },
             //显示任务详情对话框方法
             detailDialogShowMethod(task_details) {

                 //对话框进行双向绑定的显示文本等于当前传入文本
                 this.detailDialogText = task_details
                 //对话框可见
                 this.dialogDetailsVisible = true
             },
             //显示添加任务显示框
             formDialogShowMethod() {
                 //清空addForm
                 this.addForm.task_name = ""
                 this.addForm.task_details = ""
                 this.addForm.start_time = null
                 this.addForm.end_time = null
                 this.addForm.deleted_time = null
                 this.addForm.finished_time = null
                 this.addForm.task_level = 0
                 this.addForm.completion_status = 0
                 //添加任务对话框可见
                 this.dialogFormVisible = true
                 //现在进行的是添加操作
                 this.isAdd = true
             },
             //编辑对话框
             detailDialogEditMethod(index, item) {
                 this.dialogFormVisible = true
                 this.isAdd = false
                 // var obj = JSON.parse(JSON.stringify(item))
                 this.editIndex = index
                 //将传入的对象渲染表单对象
                 this.addForm = JSON.parse(JSON.stringify(item))

             },
             //从已删除列表中还原到未完成列表
             reductionFromDeletedToUnfinish(index, item) {
                 //清空删除时间
                 item.deleted_time = null
                 //把当前item添加到未完成列表
                 this.unfinishTaskList.push(JSON.parse(JSON.stringify(item)))
                 //从已删除列表移除
                 this.deletedTaskList.splice(index, 1)

             },
             //从已删除列表中彻底删除
             deletedFromDeletedTaskList(index, item) {
                 this.deletedTaskList.splice(index, 1)
             },
             //从已完成列表中还原到未完成列表
             restoreFromFinishTaskList(index, item) {
                 //将完成时间设为空
                 item.finished_time = null
                 //从已完成列表中删除
                 this.finishTaskList.splice(index, 1)
                 //重新添加到未完成列表
                 this.unfinishTaskList.push(JSON.parse(JSON.stringify(item)))

             },
             //从已完成列表中删除,到垃圾桶中（已删除）
             deletedFromFinishTaskList(index, item) {
                 //设置删除时间
                 item.deleted_time = new Date()
                 //添加到已删除集合
                 this.deletedTaskList.push(JSON.parse(JSON.stringify(item)))
                 //从已完成集合中删除
                 this.finishTaskList.splice(index, 1)
             },
             //添加任务
             addTask() {
                 //判断是否为空，是否是添加需求,添加则是push
                 if (this.isAdd) {
                     var resmble = JSON.parse(JSON.stringify(this.addForm))
                     this.unfinishTaskList.push(resmble)
                     this.isAdd = false

                     // this.addForm.task_name = this.addForm.task_details = ""
                     // this.addForm.task_level = this.addForm.completion_status = 0
                     // this.addForm.start_time = this.addForm.end_time = null
                     // this.dialogFormVisible = false
                 } else if (!this.isAdd) {
                     //否则是splice
                     //进行替换
                     this.unfinishTaskList.splice(this.editIndex, 1, JSON.parse(JSON.stringify(this.addForm)))
                     this.editIndex = 0
                 }
                 //清空addForm
                 this.addForm.task_name = ""
                 this.addForm.task_details = ""
                 this.addForm.start_time = null
                 this.addForm.end_time = null
                 this.addForm.deleted_time = null
                 this.addForm.finished_time = null
                 this.addForm.task_level = 0
                 this.addForm.completion_status = 0
                 // this.addForm = null
                 this.dialogFormVisible = false
                 // this.addForm = null

             },

             //根据输入筛选符合要求的列表
             searchFromKeyWords(keywords) {
                 //定义一个空数组，返回用
                 var newList = []
                 //循环遍历数组
                 this.unfinishTaskList.forEach(item => {
                     //如果包含
                     if (item.task_name.indexOf(keywords) != -1) {
                         //push进入新数组
                         newList.push(item)
                     }
                 })
                 return newList
             },
             //删除任务
             deleteTask(index, item) {
                 // var obj = this.unfinishTaskList.find(index)
                 //把任务从未完成列表删除
                 this.unfinishTaskList.splice(index, 1)
                 //设定删除时间等于现在操作时间
                 item.deleted_time = new Date()
                 //将当前任务添加进已删除列表
                 this.deletedTaskList.push(JSON.parse(JSON.stringify(item)))

                 //表单清空
                 this.addForm.task_name = ""
                 this.addForm.task_details = ""
                 this.addForm.start_time = null
                 this.addForm.end_time = null
                 this.addForm.deleted_time = null
                 this.addForm.finished_time = null
                 this.addForm.task_level = 0
                 this.addForm.completion_status = 0
                 // this.addForm = null
                 //表格隐藏
                 this.dialogFormVisible = false
             },
             //完成任务
             finishTask(index, item) {
                 //设置完成时间为当前操作时间
                 item.finished_time = new Date()
                 //添加到已完成列表中去
                 this.finishTaskList.push(JSON.parse(JSON.stringify(item)))
                 //从未完成中删除
                 this.unfinishTaskList.splice(index, 1)

             },
             //根据任务重要程度排序
             sortByTask_level() {
                 this.unfinishTaskList.sort(this.listElementsCompare('task_level'))
             },
             //根据任务完成度排序
             sortByCompletion_status() {
                 this.unfinishTaskList.sort(this.listElementsCompare('completion_status'))
             },
             //对象元素比较函数,上面两个排序函数的基础
             //第二个参数是一个布尔值，true或者不填都表示升序排序，false表示降序排序
             listElementsCompare(property) {
                 //  //如果未定义，则为1（升序）
                 //  if (rev == undefined) {
                 //      rev = 1
                 //  } else {
                 //      //三目运算符，rev为true则1否则-1
                 //      rev = (rev) ? 1 : -1
                 //  }

                 return function (a, b) {
                     var value1 = a[property]
                     var value2 = b[property]
                     //  if(a<b){
                     //      return rev * -1
                     //  }
                     //  if(a>b){
                     //      return rev * 1
                     //  }
                     //  return 0
                     return value2 - value1
                 }
             },
             //取消表单对话框
             cancelTask() {
                 // this.addForm = null

                 this.dialogFormVisible = false
             }
         },
         //过滤器
         filters: {
             //格式化时间
             dateFormat: function (datestr) {
                 var dt = new Date(datestr)

                 var year = dt.getFullYear()
                 var month = dt.getMonth() + 1
                 var day = dt.getDate()

                 return `${year}-${month}-${day}`
             },
             //文本话任务情况
             jugementTaskLevel: function (num) {
                 if (num == 0 || num == '0') {
                     return '一般任务'
                 }
                 if (num == 1 || num == '1') {
                     return '较为重要'
                 }
                 if (num == 2 || num == '2') {
                     return '急需完成'
                 }
             }
         }
     };
 </script>

 <style>

 </style>