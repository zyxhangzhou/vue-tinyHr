<template>
  <div style="margin-top: 20px;">
    <div style="display: flex; justify-content: space-between;">
      <div>
        <el-input placeholder="请输入员工名进行搜索..." prefix-icon="el-icon-search"
                  clearable
                  @clear="initEmps"
                  style="width: 400px; margin-right: 8px;" v-model="keyword"
                  @keydown.enter.native="initEmps"></el-input>
        <el-button icon="el-icon-search" type="primary" @click="initEmps" plain>搜索</el-button>
        <el-button type="primary">
          <i class="fa fa-angle-double-down" aria-hidden="true"></i>
          高级搜索
        </el-button>
      </div>
      <div>
        <el-upload
            :show-file-list="false"
            :before-upload="beforeUpload"
            :on-success="onSuccess"
            :on-error="onError"
            :disabled="importDataDisabled"
            style="display: inline-flex; margin-right: 9px;"
            action="/emp/basic/import">
          <el-button :disabled="importDataDisabled" type="success" :icon="importDataBtnIcon" plain>{{ importDataBtnText }}</el-button>
        </el-upload>
        <el-button type="success" icon="el-icon-download" @click="exportData" plain>
          导出数据
        </el-button>
        <el-button type="primary" icon="el-icon-plus" @click="showAddEmpView" plain>
          添加用户
        </el-button>
      </div>
    </div>
    <div style="margin-top: 20px;">
      <el-table
          :data="emps"
          stripe
          border
          style="width: 100%">
        <el-table-column
            type="selection"
            width="44">
        </el-table-column>
        <el-table-column
            fixed
            align="center"
            prop="name"
            label="姓名">
        </el-table-column>
        <el-table-column
            prop="workId"
            label="工号"
            align="center"
            width="88">
        </el-table-column>
        <el-table-column
            prop="birthday"
            align="center"
            label="出生日期"
            width="100">
        </el-table-column>
        <el-table-column
            prop="idCard"
            align="center"
            label="身份证号码"
            width="170">
        </el-table-column>
        <el-table-column
            prop="wedlock"
            align="center"
            label="婚姻情况">
        </el-table-column>
        <el-table-column
            prop="nation.name"
            align="center"
            label="民族">
        </el-table-column>
        <el-table-column
            prop="nativePlace"
            align="center"
            label="籍贯">
        </el-table-column>
        <el-table-column
            prop="politicsstatus.name"
            align="center"
            label="政治面貌">
        </el-table-column>
        <el-table-column
            prop="email"
            align="center"
            width="200"
            label="电子邮件">
        </el-table-column>
        <el-table-column
            prop="address"
            align="center"
            width="300"
            label="联系地址">
        </el-table-column>
        <el-table-column
            prop="department.name"
            align="center"
            label="所属部门">
        </el-table-column>
        <el-table-column
            prop="position.name"
            align="center"
            width="100"
            label="职位">
        </el-table-column>
        <el-table-column
            prop="jobLevel.name"
            align="center"
            width="120"
            label="职称">
        </el-table-column>
        <el-table-column
            prop="engageForm"
            align="center"
            label="聘用形式">
        </el-table-column>
        <el-table-column
            prop="beginDate"
            align="center"
            width="100"
            label="入职日期">
        </el-table-column>
        <el-table-column
            prop="conversionTime"
            align="center"
            width="100"
            label="转正日期">
        </el-table-column>
        <el-table-column
            prop="beginContract"
            align="center"
            width="110"
            label="合同起始日期">
        </el-table-column>
        <el-table-column
            prop="endContract"
            align="center"
            width="110"
            label="合同终止日期">
        </el-table-column>
        <el-table-column
            prop="contractTerm"
            align="center"
            width="90"
            label="合同期限">
          <template slot-scope="scope">
            <el-tag size="small">{{ scope.row.contractTerm }}</el-tag>
            年
          </template>
        </el-table-column>
        <el-table-column
            prop="tiptopDegree"
            align="center"
            width="50"
            label="最高学历">
        </el-table-column>
        <el-table-column
            prop="specialty"
            align="center"
            width="120"
            label="专业">
        </el-table-column>
        <el-table-column
            prop="school"
            align="center"
            width="140"
            label="毕业院校">
        </el-table-column>
        <el-table-column
            fixed="right"
            align="center"
            width="200"
            label="操作">
          <template slot-scope="scope">
            <el-button style="padding: 4px;" size="small" type="primary" @click="showEditEmpView(scope.row)" plain>编辑
            </el-button>
            <el-button style="padding: 4px;" size="small" type="primary">查看高级资料</el-button>
            <el-button style="padding: 4px;" size="small" type="danger" @click="deleteEmp(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div style="display: flex; justify-content: center; margin-top: 9px;">
        <el-pagination
            background
            @current-change="handleCurrentChange"
            @size-change="handleSizeChange"
            layout="sizes, prev, pager, next, jumper, ->, total, slot"
            :total="total">
        </el-pagination>
      </div>
    </div>
    <el-dialog
        :title="title"
        :visible.sync="dialogVisible"
        width="70%">
      <div>
        <el-form :model="emp" :rules="rules" ref="empForm">
          <el-row>
            <el-col :span="6">
              <el-form-item label="姓名：" prop="name">
                <el-input size="mini" style="width: 150px;" prefix-icon="el-icon-user-solid" v-model="emp.name"
                          placeholder="请输入姓名"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="5">
              <el-form-item label="性别：" prop="gender">
                <el-radio-group v-model="emp.gender">
                  <el-radio label="男">男</el-radio>
                  <el-radio label="女">女</el-radio>
                </el-radio-group>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="出生日期：" prop="birthday">
                <el-date-picker
                    v-model="emp.birthday"
                    size="mini"
                    type="date"
                    value-format="yyyy-MM-dd"
                    with="150px"
                    placeholder="出生日期">
                </el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :span="7">
              <el-form-item label="政治面貌：" prop="politicId">
                <el-select v-model="emp.politicId" placeholder="政治面貌" size="mini" style="width: 200px">
                  <el-option
                      v-for="item in politicsstatus"
                      :key="item.id"
                      :label="item.name"
                      :value="item.id">
                  </el-option>
                </el-select>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="6">
              <el-form-item label="民族：" prop="nationId">
                <el-select v-model="emp.nationId" placeholder="民族" size="mini" style="width: 150px">
                  <el-option
                      v-for="item in nations"
                      :key="item.id"
                      :label="item.name"
                      :value="item.id">
                  </el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="5">
              <el-form-item label="籍贯：" prop="nativePlace">
                <el-input size="mini" style="width: 120px;" prefix-icon="el-icon-location" v-model="emp.nativePlace"
                          placeholder="请输入籍贯"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="电子邮箱：" prop="email">
                <el-input size="mini" style="width: 150px;" prefix-icon="el-icon-message" v-model="emp.email"
                          placeholder="请输入电子邮箱"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="7">
              <el-form-item label="联系地址：" prop="address">
                <el-input size="mini" style="width: 200px;" prefix-icon="el-icon-office-building" v-model="emp.address"
                          placeholder="请输入联系地址"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="6">
              <el-form-item label="职位：" prop="posId">
                <el-select v-model="emp.posId" placeholder="职位" size="mini" style="width: 150px">
                  <el-option
                      v-for="item in positions"
                      :key="item.id"
                      :label="item.name"
                      :value="item.id">
                  </el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="5">
              <el-form-item label="职称：" prop="jobLevelId">
                <el-select v-model="emp.jobLevelId" placeholder="职称" size="mini" style="width: 150px">
                  <el-option
                      v-for="item in joblevels"
                      :key="item.id"
                      :label="item.name"
                      :value="item.id">
                  </el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="所属部门：" prop="departmentId">
                <el-popover
                    placement="right"
                    title="请选择部门"
                    width="200"
                    trigger="manual"
                    v-model="popVisible">

                  <el-tree default-expand-all :data="allDeps" :props="defaultProps"
                           @node-click="handleNodeClick"></el-tree>

                  <div slot="reference" class="departmentClass" @click="showDepView">
                    {{ inputDepName === '' ? '请选择部门...' : inputDepName }}
                  </div>
                </el-popover>
              </el-form-item>
            </el-col>
            <el-col :span="7">
              <el-form-item label="电话号码：" prop="phone">
                <el-input size="mini" style="width: 200px;" prefix-icon="el-icon-phone" v-model="emp.phone"
                          placeholder="请输入电话号码"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="6">
              <el-form-item label="工号：" prop="workId">
                <el-input size="mini" style="width: 150px;" prefix-icon="el-icon-edit" v-model="emp.workId"
                          placeholder="所属部门" disabled></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="5">
              <el-form-item label="学历：" prop="tiptopDegree">
                <el-select v-model="emp.tiptopDegree" placeholder="学历" size="mini" style="width: 150px">
                  <el-option
                      v-for="item in tiptopDegrees"
                      :key="item"
                      :label="item"
                      :value="item">
                  </el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="毕业院校：" prop="school">
                <el-input size="mini" style="width: 150px;" prefix-icon="el-icon-school" v-model="emp.school"
                          placeholder="毕业院校名称"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="7">
              <el-form-item label="专业名称：" prop="specialty">
                <el-input size="mini" style="width: 200px;" prefix-icon="el-icon-aim" v-model="emp.specialty"
                          placeholder="请输入专业名称"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="6">
              <el-form-item label="入职日期：" prop="beginDate">
                <el-date-picker
                    v-model="emp.beginDate"
                    size="mini"
                    type="date"
                    value-format="yyyy-MM-dd"
                    placeholder="入职日期">
                </el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="转正日期：" prop="conversionTime">
                <el-date-picker
                    v-model="emp.conversionTime"
                    size="mini"
                    type="date"
                    value-format="yyyy-MM-dd"
                    placeholder="转正日期">
                </el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="合同起始日期：" prop="beginContract">
                <el-date-picker
                    v-model="emp.beginContract"
                    size="mini"
                    type="date"
                    value-format="yyyy-MM-dd"
                    placeholder="合同起始日期">
                </el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="合同终止日期：" prop="endContract">
                <el-date-picker
                    v-model="emp.endContract"
                    size="mini"
                    type="date"
                    value-format="yyyy-MM-dd"
                    placeholder="合同终止日期">
                </el-date-picker>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="8">
              <el-form-item label="身份证号码：" prop="idCard">
                <el-input size="small" style="width: 180px;" prefix-icon="el-icon-s-check" v-model="emp.idCard"
                          placeholder="请输入身份证号码"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="8">
              <el-form-item label="聘用形式：" prop="engageForm">
                <el-radio-group v-model="emp.engageForm">
                  <el-radio label="劳动合同">劳动合同</el-radio>
                  <el-radio label="劳务合同">劳务合同</el-radio>
                </el-radio-group>
              </el-form-item>
            </el-col>
            <el-col :span="8">
              <el-form-item label="婚姻状况：" prop="wedlock">
                <el-radio-group v-model="emp.wedlock">
                  <el-radio label="已婚">已婚</el-radio>
                  <el-radio label="未婚">未婚</el-radio>
                  <el-radio label="离异">离异</el-radio>
                </el-radio-group>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form>

      </div>
      <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false; popVisible = false">取 消</el-button>
    <el-button type="primary" @click="doAddEmp">确 定</el-button>
  </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "EmpBasic",
  data() {
    return {
      title: '',
      importDataBtnText: '导入数据',
      importDataBtnIcon: 'el-icon-upload2',
      importDataDisabled: false,
      allDeps: [],
      emps: [],
      total: 0,
      page: 1,
      size: 10,
      nations: [],
      joblevels: [],
      politicsstatus: [],
      positions: [],
      tiptopDegrees: ['本科', '大专', '硕士', '博士', '高中', '初中', '小学', '其他'],
      keyword: '',
      dialogVisible: false,
      popVisible: false,
      options: [{
        value: '选项1',
        label: '黄金糕'
      }, {
        value: '选项2',
        label: '双皮奶'
      }, {
        value: '选项3',
        label: '蚵仔煎'
      }, {
        value: '选项4',
        label: '龙须面'
      }, {
        value: '选项5',
        label: '北京烤鸭'
      }],
      inputDepName: '',
      emp: {
        name: "",
        gender: "",
        birthday: "",
        idCard: "",
        wedlock: "",
        nationId: 1,
        nativePlace: "",
        politicId: 13,
        email: "",
        phone: "",
        address: "",
        departmentId: null,
        jobLevelId: 9,
        posId: 29,
        engageForm: "",
        tiptopDegree: "",
        specialty: "",
        school: "",
        beginDate: "",
        workID: "",
        contractTerm: 2,
        conversionTime: "",
        notworkDate: null,
        beginContract: "",
        endContract: "",
        workAge: null,
      },
      defaultProps: {
        children: 'children',
        label: 'name'
      },
      rules: {
        name: [{required: true, message: "请输入用户名", trigger: "blur"}],
        gender: [{required: true, message: "请输入性别", trigger: "blur"}],
        birthday: [{required: true, message: "请输入出生日期", trigger: "blur"},],
        idCard: [{required: true, message: "请输入身份证号码", trigger: "blur"},
          {
            pattern: /(^[1-9]\d{5}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$)|(^[1-9]\d{5}\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{2}$)/,
            message: "身份证号码格式不正确",
            trigger: "blur",
          },
        ],
        wedlock: [{required: true, message: "请输入婚姻状况", trigger: "blur"},],
        nationId: [{required: true, message: "请输入您组", trigger: "blur"}],
        nativePlace: [{required: true, message: "请输入籍贯", trigger: "blur"},],
        politicId: [{required: true, message: "请输入政治面貌", trigger: "blur"},],
        email: [{required: true, message: "请输入邮箱地址", trigger: "blur"},
          {
            type: "email",
            message: "邮箱格式不正确",
            trigger: "blur",
          },
        ],
        phone: [{required: true, message: "请输入电话号码", trigger: "blur"}],
        address: [{required: true, message: "请输入员工地址", trigger: "blur"},],
        departmentId: [{required: true, message: "请输入部门名称", trigger: "blur"},],
        jobLevelId: [{required: true, message: "请输入职称", trigger: "blur"},],
        posId: [{required: true, message: "请输入职位", trigger: "blur"}],
        engageForm: [{required: true, message: "请输入聘用形式", trigger: "blur"},],
        tiptopDegree: [{required: true, message: "请输入学历", trigger: "blur"},],
        specialty: [{required: true, message: "请输入专业", trigger: "blur"}],
        school: [{required: true, message: "请输入毕业院校", trigger: "blur"},],
        beginDate: [{required: true, message: "请输入入职日期", trigger: "blur"},],
        workID: [{required: true, message: "请输入工号", trigger: "blur"}],
        contractTerm: [{required: true, message: "请输入合同期限", trigger: "blur"},],
        conversionTime: [{required: true, message: "请输入转正日期", trigger: "blur"},],
        notworkDate: [{required: true, message: "请输入离职日期", trigger: "blur"},],
        beginContract: [{required: true, message: "请输入合同起始日期", trigger: "blur"},],
        endContract: [{required: true, message: "请输入合同结束日期", trigger: "blur"},],
        workAge: [{required: true, message: "请输入工龄", trigger: "blur"}],
      },
    }
  },
  mounted() {
    this.initEmps()
    this.initData()
  },
  methods: {
    onError(err, file, fileList) {
      this.importDataBtnText = '导入数据'
      this.importDataBtnIcon = 'el-icon-upload2'
      this.importDataDisabled = false
    },
    onSuccess(resp, file, fileList) {
      this.importDataBtnText = '导入数据'
      this.importDataBtnIcon = 'el-icon-upload2'
      this.importDataDisabled = false
      this.initEmps()
    },
    beforeUpload() {
      this.importDataBtnText = '正在导入...'
      this.importDataBtnIcon = 'el-icon-loading'
      this.importDataDisabled = true
    },
    exportData() {
      window.open('/emp/basic/export', '_parent')
    },
    emptyEmp() {
      this.emp = {
        name: "",
        gender: "",
        birthday: "",
        idCard: "",
        wedlock: "",
        nationId: 1,
        nativePlace: "",
        politicId: 13,
        email: "",
        phone: "",
        address: "",
        departmentId: null,
        jobLevelId: 9,
        posId: 29,
        engageForm: "",
        tiptopDegree: "",
        specialty: "",
        school: "",
        beginDate: "",
        workID: "",
        contractTerm: 2,
        conversionTime: "",
        notworkDate: null,
        beginContract: "",
        endContract: "",
        workAge: null,
      };
      // this.inputDepName = "";
    },
    showEditEmpView(data) {
      this.initPositions();
      this.title = "编辑员工信息";
      this.emp = data;
      this.dialogVisible = true;
    },
    deleteEmp(data) {
      this.$confirm('此操作将永久删除【' + data.name + '】用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/emp/basic/' + data.id).then(resp => {
          if (resp) {
            this.initEmps()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    doAddEmp() {
      if (this.emp.id) {
        this.$refs["empForm"].validate((valid) => {
          if (valid) {
            this.putRequest("/emp/basic/", this.emp).then((resp) => {
              if (resp) {
                this.dialogVisible = false;
                this.initEmps();
              }
            });
          }
        });
      } else {
        this.$refs["empForm"].validate((valid) => {
          if (valid) {
            this.postRequest("/emp/basic/", this.emp).then((resp) => {
              if (resp) {
                this.dialogVisible = false;
                this.initEmps();
              }
            });
          }
        });
      }
    },
    handleNodeClick(data) {
      this.inputDepName = data.name
      this.emp.departmentId = data.id
      this.popVisible = !this.popVisible
    },
    showDepView() {
      this.popVisible = !this.popVisible
    },
    initPositions() {
      this.getRequest('/emp/basic/positions').then(resp => {
        if (resp) this.positions = resp;
      })
    },
    getMaxWorkID() {
      this.getRequest('/emp/basic/maxWorkID').then(resp => {
        if (resp) this.emp.workId = resp.obj
      })
    },
    initData() {
      if (!window.sessionStorage.getItem("nations")) {
        this.getRequest('/emp/basic/nations').then(resp => {
          if (resp) {
            this.nations = resp;
            window.sessionStorage.setItem('nations', JSON.stringify(resp))
          }
        })
      } else {
        this.nations = JSON.parse(window.sessionStorage.getItem('nations'))
      }
      if (!window.sessionStorage.getItem("joblevels")) {
        this.getRequest('/emp/basic/joblevels').then(resp => {
          if (resp) {
            this.joblevels = resp;
            window.sessionStorage.setItem('joblevels', JSON.stringify(resp))
          }
        })
      } else {
        this.joblevels = JSON.parse(window.sessionStorage.getItem('joblevels'))
      }
      if (!window.sessionStorage.getItem("politicsstatus")) {
        this.getRequest('/emp/basic/politicsstatus').then(resp => {
          if (resp) {
            this.politicsstatus = resp;
            window.sessionStorage.setItem('politicsstatus', JSON.stringify(resp))
          }
        })
      } else {
        this.politicsstatus = JSON.parse(window.sessionStorage.getItem('politicsstatus'))
      }
      if (!window.sessionStorage.getItem("deps")) {
        this.getRequest('/emp/basic/deps').then(resp => {
          if (resp) {
            this.allDeps = resp;
            window.sessionStorage.setItem('deps', JSON.stringify(resp))
          }
        })
      } else {
        this.allDeps = JSON.parse(window.sessionStorage.getItem('deps'))
      }
    },
    showAddEmpView() {
      this.emptyEmp()
      this.title = '添加员工'
      this.initPositions()
      this.getMaxWorkID()
      this.dialogVisible = true
    },
    handleSizeChange(currentSize) {
      this.size = currentSize
      this.initEmps()
    },
    handleCurrentChange(currentPage) {
      this.page = currentPage
      this.initEmps()
    },
    initEmps() {
      this.getRequest('/emp/basic/?page=' + this.page + '&size=' + this.size + '&keyword=' + this.keyword).then(resp => {
        if (resp) {
          this.emps = resp.data
          this.total = resp.total
        }
      })
    }
  }
}

</script>

<style>
.departmentClass {
  width: 150px;
  display: inline-flex;
  font-size: 13px;
  border: 1px solid #dedede;
  height: 26px;
  border-radius: 5px;
  margin-top: 7px;
  cursor: pointer;
  align-items: center;
  padding-left: 5px;
  box-sizing: border-box;
}
</style>