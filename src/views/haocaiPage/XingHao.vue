<template>
  <div class="userCustomMobanPage1">
    <div class="userCustomMobanPageContainer">
      <div class="userCustomMobanPageContainer-buttons">
        <el-button @click="viewOnQueryEvent">查 询</el-button>
        <el-button @click="viewOnResetEvent">重 置</el-button>
      </div>
      <div class="userCustomMobanPageContainer-contents">
        <div class="userCustomMobanPageContainer-conditions">
          <el-form label-width="30%" :inline="true" :model="conditionsForm"
                   class="demo-form-inline userCustomMobanPageContainer-conditions__form">
            <el-row>

              <el-col :span="8">
                <!--<el-form-item label="类型:">-->
                <!--<HyhtSelect :modelSelectValue='initConditionsF.type'></HyhtSelect>-->
                <!--</el-form-item>-->
                <el-form-item label="类型:">
                  <HyhtSelect @change-event="pushConditionsBrandOpt"
                              :modelSelectValue='initConditionsF.type'></HyhtSelect>
                </el-form-item>


              </el-col>
              <el-col :span="8">

                <el-form-item label="品牌:">
                  <!--<HyhtSelect :modelSelectValue='initConditionsF.brand'></HyhtSelect>-->
                  <!--@change-event="pushConditionsModelOpt"-->
                  <HyhtSelect :disabled-val="disableVal.searchBDV" :modelSelectValue='initConditionsF.brand'></HyhtSelect>
                </el-form-item>

              </el-col>
              <el-col :span="8">
                <el-form-item label="型号:">
                  <!--<HyhtSelect :disabled-val="disableVal.searchMDV" :modelSelectValue='initConditionsF.model'></HyhtSelect>-->
                  <!--<HyhtSelect :disabled-val="disableVal.searchMDV"-->
                              <!--:modelSelectValue='initConditionsF.model'></HyhtSelect>-->
                  <HyhtInput :inputValue='initConditionsF.model'></HyhtInput>
                </el-form-item>
              </el-col>

            </el-row>
          </el-form>
        </div>


        <div class="userCustomMobanPageContainer-buttons">
          <el-button :disabled="multipleSel.length === 0" @click="batchDel()">批量删除</el-button>
          <el-button @click="addPop()">新增</el-button>

        </div>
        <div class="userCustomMobanPageContainer-tables">
          <el-table
            :data="tableData"
            ref="multipleTable"
            @selection-change="selectionChange"
            border
            style="width: 100%">
            <el-table-column align="center" type="selection">
            </el-table-column>
            <el-table-column align="center"
                             prop="type.label"
                             label="类型">
            </el-table-column>
            <el-table-column align="center"
                             prop="brand.label"
                             label="品牌">
            </el-table-column>
            <el-table-column align="center"
                             prop="model"
                             label="型号">
            </el-table-column>
            <el-table-column align="center"
                             prop="time"
                             label="创建时间"
            >
            </el-table-column>


            <el-table-column label="操作" align="center">
              <template slot-scope="scope">

                <el-button @click="amendPop(scope.row)"
                           size="small" type="text">修改
                </el-button>
                <el-button @click="delRow(scope.row)"
                           size="small"
                           type="text"
                           class="operationAbnormalColor">删除
                </el-button>

              </template>
            </el-table-column>
          </el-table>
          <div class="userCustomMobanPageContainer-paginations">
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="currentPage"
              :page-size="pageSize"
              layout="total, prev, pager, next"
              :total="total"
              background>
            </el-pagination>
          </div>
        </div>
      </div>

      <!--修改弹窗-->
      <div class="userCustomMobanPageContainer-msgDialog">
        <el-dialog title="修改" :visible.sync="amendFormVisible" width="40%" :before-close="handleCloseDialog">
          <div class="userCustomMobanPageContainer-msgDialog__btns">
            <el-button @click="viewOnAffirmEvent">确 认</el-button>
            <el-button @click="amendFormVisible = false">取 消</el-button>
          </div>
          <div class="userCustomMobanPageContainer-msgDialog__body">
            <div class="userCustomMobanPageContainer-msgDialog__bodyTitle">修改</div>
            <div class="userCustomMobanPageContainer-msgDialog__bodyContent">
              <div class="userCustomMobanPageContainer-conditions">
                <el-form label-width="35%" :inline="true" :model="amendForm"
                         class="demo-form-inline userCustomMobanPageContainer-conditions__form">
                  <el-row>
                    <el-col :span="24">
                      <el-form-item label="类型:">
                        <HyhtSelect @change-event="pushAmendBrandOpt" :modelSelectValue='initAmendF.type'></HyhtSelect>
                      </el-form-item>
                    </el-col>
                    <el-col :span="24">
                      <el-form-item label="品牌:">
                        <HyhtSelect :disabled-val="disableVal.amendBDV" :modelSelectValue='initAmendF.brand'></HyhtSelect>

                      </el-form-item>
                    </el-col>
                    <el-col :span="24">
                      <el-form-item label="型号:">
                        <!--<HyhtSelect :modelSelectValue='initAmendF.model'></HyhtSelect>-->
                        <HyhtInput :inputValue='initAmendF.model'></HyhtInput>
                      </el-form-item>
                    </el-col>

                    <!--<el-col :span="24">-->
                    <!--<el-form-item label="创建时间:">-->
                    <!--<HyhtDate :modelHyhtDateVal='initAmendF.time'></HyhtDate>-->
                    <!--</el-form-item>-->

                    <!--</el-col>-->


                  </el-row>
                </el-form>
              </div>
            </div>
          </div>
        </el-dialog>
      </div>

      <!--新增弹窗-->

      <div class="userCustomMobanPageContainer-msgDialog">
        <el-dialog title="新增" :visible.sync="addFormVisible" width="40%" :before-close="handleCloseDialog">
          <div class="userCustomMobanPageContainer-msgDialog__btns">
            <el-button @click="confirmAdd()">确 认</el-button>
            <el-button @click="addFormVisible = false">取消</el-button>
          </div>
          <div class="userCustomMobanPageContainer-msgDialog__body">
            <div class="userCustomMobanPageContainer-msgDialog__bodyTitle">新增</div>
            <div class="userCustomMobanPageContainer-msgDialog__bodyContent">
              <div class="userCustomMobanPageContainer-conditions">
                <el-form label-width="35%" :inline="true" :model="addForm"
                         class="demo-form-inline userCustomMobanPageContainer-conditions__form">
                  <el-row>
                    <el-col :span="24">
                      <el-form-item label="类型:">

                        <el-tooltip v-if="initAddForm.type.selectOptions.length <= 0" class="item" effect="dark"
                                    content="没有任何类型，请先到类型维护添加类型" placement="right">

                          <HyhtSelect :disabled-val="true" :modelSelectValue='initAddForm.type'></HyhtSelect>

                        </el-tooltip>
                        <!--<HyhtSelect v-else :modelSelectValue='initAddForm.type'></HyhtSelect>-->
                        <HyhtSelect @change-event="pushAddBrandOpt" v-else
                                    :modelSelectValue='initAddForm.type'></HyhtSelect>


                      </el-form-item>
                    </el-col>
                    <el-col :span="24">
                      <el-form-item label="品牌:">

                        <el-tooltip v-if="initAddForm.type.modelValue!=''&&initAddForm.brand.selectOptions.length <= 0"
                                    class="item" effect="dark" content="没有任何品牌，请先到品牌维护添加品牌" placement="right">

                          <HyhtSelect :disabled-val="true" :modelSelectValue='initAddForm.brand'></HyhtSelect>

                        </el-tooltip>
                        <HyhtSelect :disabled-val="disableVal.addBDV" v-else
                                    :modelSelectValue='initAddForm.brand'></HyhtSelect>

                      </el-form-item>
                    </el-col>
                    <el-col :span="24">
                      <el-form-item label="型号:">

                        <HyhtInput :inputValue='initAddForm.model'></HyhtInput>
                      </el-form-item>
                    </el-col>

                    <!--<el-col :span="24">-->
                    <!--<el-form-item label="创建时间:">-->
                    <!--<HyhtDate :modelHyhtDateVal='initAddForm.time'></HyhtDate>-->
                    <!--</el-form-item>-->

                    <!--</el-col>-->


                  </el-row>
                </el-form>
              </div>
            </div>
          </div>
        </el-dialog>
      </div>


    </div>
  </div>
</template>
<script>
  import HyhtInput from '../../components/allConditionmodel/HyhtInput'
  import HyhtSelect from '../../components/allConditionmodel/HyhtSelect'
  import HyhtMupSelect from '../../components/allConditionmodel/HyhtMupSelect'
  import HyhtCheckSel from '../../components/allConditionmodel/HyhtCheckSel'
  import HyhtCascader from '../../components/allConditionmodel/HyhtCascader'
  import HyhtDate from '../../components/allConditionmodel/HyhtDate'
  import HyhtDateTime from '../../components/allConditionmodel/HyhtDateTime'
  import HyhtDateWeek from '../../components/allConditionmodel/HyhtDateWeek'
  import HyhtDateMonth from '../../components/allConditionmodel/HyhtDateMonth'
  import HyhtDataYear from '../../components/allConditionmodel/HyhtDataYear'
  import HyhtDaterange from '../../components/allConditionmodel/HyhtDaterange'

  export default {
    name: "userCustomMobanPage1",
    components: {
      HyhtInput, HyhtSelect, HyhtMupSelect, HyhtCheckSel, HyhtCascader,
      HyhtDate, HyhtDateTime, HyhtDateWeek, HyhtDateMonth, HyhtDataYear,
      HyhtDaterange
    },
    data() {
      return {

        // 联动下拉选项
        typeObj: [
          {
            id: '1',
            name: '类型1',
            checked: false,
            brand: [
              {
                id: '1',
                name: 't1_品牌1',
                checked: false,
                model: [
                  {id: '1', name: 't1_b1_型号1', checked: false},
                  {id: '2', name: 't1_b1_型号2', checked: false},
                  {id: '3', name: 't1_b1_型号3', checked: false}
                ]
              },
              {
                id: '2',
                name: 't1_品牌2',
                checked: false,
                model: [
                  {id: '1', name: 't1_b2_型号1', checked: false},
                  {id: '2', name: 't1_b2_型号2', checked: false},
                  {id: '3', name: 't1_b2_型号3', checked: false}
                ]
              },
              {
                id: '3',
                name: 't1_品牌3',
                checked: false,
                model: [
                  {id: '1', name: 't1_b3_型号1', checked: false},
                  {id: '2', name: 't1_b3_型号2', checked: false},
                  {id: '3', name: 't1_b3_型号3', checked: false}
                ]
              },
            ]

          },
          {
            id: '2',
            name: '类型2',
            checked: false,
            brand: [
              {
                id: '1',
                name: 't2_品牌1',
                checked: false,
                model: [
                  {id: '1', name: 't2_b1_型号1', checked: false},
                  {id: '2', name: 't2_b1_型号2', checked: false},
                  {id: '3', name: 't2_b1_型号3', checked: false}
                ]
              },
              {
                id: '2',
                name: 't2_品牌2',
                checked: false,
                model: [
                  {id: '1', name: 't2_b2_型号1', checked: false},
                  {id: '2', name: 't2_b2_型号2', checked: false},
                  {id: '3', name: 't2_b2_型号3', checked: false}
                ]
              },
              {
                id: '3',
                name: 't2_品牌3',
                checked: false,
                model: [
                  {id: '1', name: 't2_b3_型号1', checked: false},
                  {id: '2', name: 't2_b3_型号2', checked: false},
                  {id: '3', name: 't2_b3_型号3', checked: false}
                ]
              },
            ]

          },
          {
            id: '3',
            name: '类型3',
            checked: false,
            brand: [
              {
                id: '1',
                name: 't3_品牌1',
                checked: false,
                model: [
                  {id: '1', name: 't3_b1_型号1', checked: false},
                  {id: '2', name: 't3_b1_型号2', checked: false},
                  {id: '3', name: 't3_b1_型号3', checked: false}
                ]
              },
              {
                id: '2',
                name: 't3_品牌2',
                checked: false,
                model: [
                  {id: '1', name: 't3_b2_型号1', checked: false},
                  {id: '2', name: 't3_b2_型号2', checked: false},
                  {id: '3', name: 't3_b2_型号3', checked: false}
                ]
              },
              {
                id: '3',
                name: 't3_品牌3',
                checked: false,
                model: [
                  {id: '1', name: 't3_b3_型号1', checked: false},
                  {id: '2', name: 't3_b3_型号2', checked: false},
                  {id: '3', name: 't3_b3_型号3', checked: false}
                ]
              },
            ]

          }
        ],//类型下的/品牌/型号
        //搜索框的 联动下拉选项
        searchTypeObj: [
          {
            id: '0',
            name: '全部',
            checked: false,
            brand: [
              {
                id: '0',
                name: '全部',
                checked: false,
                model: [
                  {id: '0', name: '全部', checked: false},
                  {id: '1_1_1', name: 't1_b1_型号1', checked: false},
                  {id: '1_1_2', name: 't1_b1_型号2', checked: false},
                  {id: '1_1_3', name: 't1_b1_型号3', checked: false},


                  {id: '1_2_1', name: 't1_b2_型号1', checked: false},
                  {id: '1_2_2', name: 't1_b2_型号2', checked: false},
                  {id: '1_2_3', name: 't1_b2_型号3', checked: false},


                  {id: '1_3_1', name: 't1_b3_型号1', checked: false},
                  {id: '1_3_2', name: 't1_b3_型号2', checked: false},
                  {id: '1_3_3', name: 't1_b3_型号3', checked: false},

                  {id: '2_1_1', name: 't2_b1_型号1', checked: false},
                  {id: '2_1_2', name: 't2_b1_型号2', checked: false},
                  {id: '2_1_3', name: 't2_b1_型号3', checked: false},

                  {id: '2_2_1', name: 't2_b2_型号1', checked: false},
                  {id: '2_2_2', name: 't2_b2_型号2', checked: false},
                  {id: '2_2_3', name: 't2_b2_型号3', checked: false},

                  {id: '2_3_1', name: 't2_b3_型号1', checked: false},
                  {id: '2_3_2', name: 't2_b3_型号2', checked: false},
                  {id: '2_3_3', name: 't2_b3_型号3', checked: false},

                  {id: '3_1_1', name: 't3_b1_型号1', checked: false},
                  {id: '3_1_2', name: 't3_b1_型号2', checked: false},
                  {id: '3_1_3', name: 't3_b1_型号3', checked: false},

                  {id: '3_2_1', name: 't3_b2_型号1', checked: false},
                  {id: '3_2_2', name: 't3_b2_型号2', checked: false},
                  {id: '3_2_3', name: 't3_b2_型号3', checked: false},

                  {id: '3_3_1', name: 't3_b3_型号1', checked: false},
                  {id: '3_3_2', name: 't3_b3_型号2', checked: false},
                  {id: '3_3_3', name: 't3_b3_型号3', checked: false}




                ]
              },

              {
                id: '1_1',
                name: 't1_品牌1',
                checked: false,
                model: [
                  {id: '1_1_1', name: 't1_b1_型号1', checked: false},
                  {id: '1_1_2', name: 't1_b1_型号2', checked: false},
                  {id: '1_1_3', name: 't1_b1_型号3', checked: false}
                ]
              },
              {
                id: '1_2',
                name: 't1_品牌2',
                checked: false,
                model: [
                  {id: '1_2_1', name: 't1_b2_型号1', checked: false},
                  {id: '1_2_2', name: 't1_b2_型号2', checked: false},
                  {id: '1_2_3', name: 't1_b2_型号3', checked: false}
                ]
              },
              {
                id: '1_3',
                name: 't1_品牌3',
                checked: false,
                model: [
                  {id: '1_3_1', name: 't1_b3_型号1', checked: false},
                  {id: '1_3_2', name: 't1_b3_型号2', checked: false},
                  {id: '1_3_3', name: 't1_b3_型号3', checked: false}
                ]
              },

              {
                id: '2_1',
                name: 't2_品牌1',
                checked: false,
                model: [
                  {id: '2_1_1', name: 't2_b1_型号1', checked: false},
                  {id: '2_1_2', name: 't2_b1_型号2', checked: false},
                  {id: '2_1_3', name: 't2_b1_型号3', checked: false}
                ]
              },
              {
                id: '2_2',
                name: 't2_品牌2',
                checked: false,
                model: [
                  {id: '2_2_1', name: 't2_b2_型号1', checked: false},
                  {id: '2_2_2', name: 't2_b2_型号2', checked: false},
                  {id: '2_2_3', name: 't2_b2_型号3', checked: false}
                ]
              },
              {
                id: '2_3',
                name: 't2_品牌3',
                checked: false,
                model: [
                  {id: '2_3_1', name: 't2_b3_型号1', checked: false},
                  {id: '2_3_2', name: 't2_b3_型号2', checked: false},
                  {id: '2_3_3', name: 't2_b3_型号3', checked: false}
                ]
              },

              {
                id: '3_1',
                name: 't3_品牌1',
                checked: false,
                model: [
                  {id: '3_1_1', name: 't3_b1_型号1', checked: false},
                  {id: '3_1_2', name: 't3_b1_型号2', checked: false},
                  {id: '3_1_3', name: 't3_b1_型号3', checked: false}
                ]
              },
              {
                id: '3_2',
                name: 't3_品牌2',
                checked: false,
                model: [
                  {id: '3_2_1', name: 't3_b2_型号1', checked: false},
                  {id: '3_2_2', name: 't3_b2_型号2', checked: false},
                  {id: '3_2_3', name: 't3_b2_型号3', checked: false}
                ]
              },
              {
                id: '3_3',
                name: 't3_品牌3',
                checked: false,
                model: [
                  {id: '3_3_1', name: 't3_b3_型号1', checked: false},
                  {id: '3_3_2', name: 't3_b3_型号2', checked: false},
                  {id: '3_3_3', name: 't3_b3_型号3', checked: false}
                ]
              },
            ]

          },
          {
            id: '1',
            name: '类型1',
            checked: false,
            brand: [
              {
                id: '1_1',
                name: 't1_品牌1',
                checked: false,
                model: [
                  {id: '1_1_1', name: 't1_b1_型号1', checked: false},
                  {id: '1_1_2', name: 't1_b1_型号2', checked: false},
                  {id: '1_1_3', name: 't1_b1_型号3', checked: false}
                ]
              },
              {
                id: '1_2',
                name: 't1_品牌2',
                checked: false,
                model: [
                  {id: '1_2_1', name: 't1_b2_型号1', checked: false},
                  {id: '1_2_2', name: 't1_b2_型号2', checked: false},
                  {id: '1_2_3', name: 't1_b2_型号3', checked: false}
                ]
              },
              {
                id: '1_3',
                name: 't1_品牌3',
                checked: false,
                model: [
                  {id: '1_3_1', name: 't1_b3_型号1', checked: false},
                  {id: '1_3_2', name: 't1_b3_型号2', checked: false},
                  {id: '1_3_3', name: 't1_b3_型号3', checked: false}
                ]
              },
            ]

          },
          {
            id: '2',
            name: '类型2',
            checked: false,
            brand: [
              {
                id: '2_1',
                name: 't2_品牌1',
                checked: false,
                model: [
                  {id: '2_1_1', name: 't2_b1_型号1', checked: false},
                  {id: '2_1_2', name: 't2_b1_型号2', checked: false},
                  {id: '2_1_3', name: 't2_b1_型号3', checked: false}
                ]
              },
              {
                id: '2_2',
                name: 't2_品牌2',
                checked: false,
                model: [
                  {id: '2_2_1', name: 't2_b2_型号1', checked: false},
                  {id: '2_2_2', name: 't2_b2_型号2', checked: false},
                  {id: '2_2_3', name: 't2_b2_型号3', checked: false}
                ]
              },
              {
                id: '2_3',
                name: 't2_品牌3',
                checked: false,
                model: [
                  {id: '2_3_1', name: 't2_b3_型号1', checked: false},
                  {id: '2_3_2', name: 't2_b3_型号2', checked: false},
                  {id: '2_3_3', name: 't2_b3_型号3', checked: false}
                ]
              },
            ]

          },
          {
            id: '3',
            name: '类型3',
            checked: false,
            brand: [
              {
                id: '3_1',
                name: 't3_品牌1',
                checked: false,
                model: [
                  {id: '3_1_1', name: 't3_b1_型号1', checked: false},
                  {id: '3_1_2', name: 't3_b1_型号2', checked: false},
                  {id: '3_1_3', name: 't3_b1_型号3', checked: false}
                ]
              },
              {
                id: '3_2',
                name: 't3_品牌2',
                checked: false,
                model: [
                  {id: '3_2_1', name: 't3_b2_型号1', checked: false},
                  {id: '3_2_2', name: 't3_b2_型号2', checked: false},
                  {id: '3_2_3', name: 't3_b2_型号3', checked: false}
                ]
              },
              {
                id: '3_3',
                name: 't3_品牌3',
                checked: false,
                model: [
                  {id: '3_3_1', name: 't3_b3_型号1', checked: false},
                  {id: '3_3_2', name: 't3_b3_型号2', checked: false},
                  {id: '3_3_3', name: 't3_b3_型号3', checked: false}
                ]
              },
            ]

          }
        ],//类型下的/品牌/型号

        // 当前选中类型下的brand
        currentBrands: {
          search: [],//查询表单中的
          add: [],//新增表单中的
          amend: []
        },
        disableVal: {
          searchBDV: true,
          searchMDV: true,
          addBDV: true,
          addMDV: true,
          amendBDV: false,
          amendMDV: false
        },

        // 搜索条件表单
        conditionsForm: {},
        //初始化搜索表单
        initConditionsF: {
          type: {
            modelValue: "0",
            selectOptions: []
          },
          brand: {
            modelValue: '0',
            selectOptions: []
          },
          model: {
            modelValue: '',
          }
        },
        tableData: [
          {
            id: '1',
            type: {value: '1', label: '类型1'},
            brand: {value: '1', label: '品牌1'},
            model:'型号1',
            time: '2019-08-28'

          }, {
            id: '2',
            type: {value: '2', label: '类型2'},
            brand: {value: '2', label: '品牌2'},
            model:'型号1',
            time: '2019-08-28'
          }, {
            id: '3',
            type: {value: '3', label: '类型3'},
            brand: {value: '1', label: '品牌1'},
            model:'型号1',
            time: '2019-08-28'
          }, {
            id: '4',
            type: {value: '1', label: '类型1'},
            brand: {value: '3', label: '品牌3'},
            model:'型号1',
            time: '2019-08-28'
          }, {
            id: '5',
            type: {value: '1', label: '类型1'},
            brand: {value: '1', label: '品牌1'},
            model:'型号1',
            time: '2019-08-28'
          }, {
            id: '6',
            type: {value: '1', label: '类型1'},
            brand: {value: '1', label: '品牌1'},
            model:'型号1',
            time: '2019-08-28'
          }, {
            id: '7',
            type: {value: '1', label: '类型1'},
            brand: {value: '1', label: '品牌1'},
            model:'型号1',
            time: '2019-08-28'
          }
        ],
        multipleSel: [],//列表的已选项
        currentPage: 1,
        total: 20,
        pageSize: 10,
        pageNo: 1,

        amendForm: {},
        amendFormVisible: false,
        //初始化修改表单
        initAmendF: {
          type: {
            modelValue: "",
            selectOptions: []
          },
          brand: {
            modelValue: "",
            selectOptions: []
          },
          model: {
            modelValue: "",
          },

          time: {modelValue: ''}

        },

        addForm: {},
        addFormVisible: false,
        initAddForm: {
          type: {
            modelValue: "",
            selectOptions: []
          },
          brand: {
            modelValue: "",
            selectOptions: []
          },
          model: {
            modelValue: "",

          },

          time: {modelValue: ''}

        },


      };
    },
    created() {

      // 获取类型
      this.initConditionsF.type.selectOptions = this.$mycommon.initOpt(this.searchTypeObj);
      this.currentBrands.search =  this.$mycommon.pushBrandOpt('0',this.searchTypeObj,this.initConditionsF,this.currentBrands.search);
      this.disableVal.searchBDV = false; //品牌选项可用


      this.initAddForm.type.selectOptions = this.$mycommon.initOpt(this.typeObj);


    },
    mounted() {
      // var that = this;

    },
    methods: {


      //查询表单添加品牌下拉项
      pushConditionsBrandOpt(value) {

        this.currentBrands.search = this.$mycommon.pushBrandOpt(value,this.searchTypeObj,this.initConditionsF,this.currentBrands.search);

        this.initConditionsF.brand.modelValue = '';
        this.initConditionsF.model.modelValue = '';
        this.disableVal.searchBDV = false; //品牌选项可用
        this.disableVal.searchMDV = true;
      },

      //新增表单添加品牌下拉项
      pushAddBrandOpt(value) {

        this.currentBrands.add = this.$mycommon.pushBrandOpt(value,this.typeObj,this.initAddForm, this.currentBrands.add);

        this.initAddForm.brand.modelValue = '';

        this.disableVal.addBDV = false; //品牌选项可用
      },

      //修改表单添加品牌下拉项
      pushAmendBrandOpt(value) {

        this.currentBrands.amend =  this.$mycommon.pushBrandOpt(value,this.typeObj,this.initAmendF,this.currentBrands.amend);

        this.initAmendF.brand.modelValue = '';
        this.disableVal.amendMDV = true; //型号选项可用
      },



      // 查询
      viewOnQueryEvent() {
        var conditionsForm = {
          type: this.initConditionsF.type,
          brand: this.initConditionsF.brand,
          model: this.initConditionsF.model
        };

        this.conditionsForm = this.$mycommon.getFormVal(conditionsForm);
        console.log("条件查询表单：", this.conditionsForm);

        // 提交完成 重置表单
        // this.viewOnResetEvent();
      },
      // 重置
      viewOnResetEvent() {
        this.initConditionsF.type.modelValue = '';
        this.initConditionsF.brand.modelValue = '';
        this.initConditionsF.model.modelValue = '';
      },


      //批量删除
      batchDel() {
        //获取 勾选的行id
        var rowIds = this.multipleSel.map(item => item.id).toString();

        this.$confirm('确认删除第' + rowIds + '行数据吗？', '提示', {
          type: 'warning'
        }).then(() => {

          this.$message({
            message: '删除第' + rowIds + '行成功',
            type: 'success'
          });

          //清空多选内容
          this.toggleSelection();
          // console.log("清空已选？",this.multipleSelection)
        }).catch(() => {

        });
      },

      // 删除行
      delRow(data) {
        // console.log(data.id);
        this.$confirm('确认删除' + data.id + '吗', '提示', {type: 'warning'}).then(() => {
          // todo:删除操作

          this.$message({message: '删除成功！', type: 'success'});

        });
      },

      // 新增
      addPop() {
        this.addFormVisible = true;

      },
      confirmAdd() {
        // console.log("新增表单",this.addForm);

        // todo:新增操作

        // 获取当前时间
        this.initAddForm.time.modelValue = this.$mycommon.getNewFormatDate();

        var addForm = {
          type: this.initAddForm.type,
          brand: this.initAddForm.brand,
          model: this.initAddForm.model,
          time: this.initAddForm.time
        };

        this.addForm = this.$mycommon.getFormVal(addForm);
        console.log("新增表单：", this.addForm);


        this.$message({message: '新增成功！', type: 'success'});

        this.addFormVisible = false;
      },


      //列表多选
      selectionChange(row) {
        this.multipleSel = row;
        // console.log(this.multipleSel.length)
      },
      toggleSelection(rows) {
        if (rows) {
          rows.forEach(row => {
            this.$refs.multipleTable.toggleRowSelection(row);

          })
        } else {
          this.$refs.multipleTable.clearSelection();
        }
      },


      // 弹出修改弹窗
      amendPop(data) {
        this.amendFormVisible = true;

        // 初始化表单下拉选项
        this.initAmendF.type.selectOptions = this.$mycommon.initOpt(this.typeObj);
        this.currentBrands.amend =  this.$mycommon.pushBrandOpt(data.type.value,this.typeObj,this.initAmendF,this.currentBrands.amend);
        // this.pushModelOpt(data.brand.value,this.currentBrands.amend,this.initAmendF);

        //初始化表单
        // console.log('修改的行数据', data, this.amendForm);
        this.initAmendF.type.modelValue = data.type.value;
        this.initAmendF.brand.modelValue = data.brand.value;
        this.initAmendF.model.modelValue = data.model;
        // this.initAmendF.time.modelValue = data.time;


      },
      // 确认修改
      viewOnAffirmEvent() {
        this.amendFormVisible = false;//关闭弹窗

        var amendForm = {
          type: this.initAmendF.type,
          brand: this.initAmendF.brand,
          model: this.initAmendF.model,
          // time:this.initAmendF.time
        };

        this.amendForm = this.$mycommon.getFormVal(amendForm);
        console.log("修改的表单", this.amendForm);


        this.$message({
          message: '修改成功！',
          type: 'success'
        });
      },

      // 取消
      viewOnCancelEvent() {
        // var that = this;
        this.userCustomOfDialog = false;
      },
      handleCloseDialog(done) {
        var that = this;
        that.$confirm('确认关闭？', '提示')
          .then(_ => {
            done();
          })
          .catch(_ => {
          });
      },


      handleSizeChange(val) {
        var that = this;
        that.pageSize = val;
      },
      handleCurrentChange(val) {
        var that = this;
        that.pageNo = val;
      },


    }
  };
</script>
<style scoped>

</style>
