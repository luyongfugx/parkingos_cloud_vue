<template>
    <section>
        <common-table
                :queryapi="queryapi"
                :addapi="addapi"
                :editapi="editapi"
                :delapi="delapi"
                :tableheight="tableheight"
                :fieldsstr="fieldsstr"
                :tableitems="tableitems"
                :btswidth="btswidth"
                :hide-export="hideExport"
                :hide-options="hideOptions"
                :addtitle="addtitle"

                :hideTool="hideTool"

                :hideSearch="hideSearch"
                :hideAdd="hideAdd"
                :showEdit="showEdit"
                :showdelete="showdelete"
                :showresetpwd="showresetpwd"
                ref="bolinkuniontable"
        ></common-table>
        <!--:searchtitle="searchtitle"-->
    </section>
</template>

<script>

    import {path, checkURL, checkUpload, checkNumber} from '../../api/api';
    import util from '../../common/js/util'
    import common from '../../common/js/common'
    import CommonTable from '../../components/CommonTable'
    import axios from 'axios'

    export default {
        components: {
            CommonTable      //表格
        },
        data() {
            return {
                loading: false,         //loading页面是否显示
                hideExport: true,       //隐藏导出
                hideSearch: true,      //隐藏查询
                 //显示日期查询
                hideAdd: false,         //隐藏添加
                tableheight: '',        //表格高度
                showdelete: true,       //显示删除
                hideOptions: false,      //隐藏多选框
                       //显示停车信息
                hideTool: false,        //隐藏工具栏
                showEdit:true,
                showresetpwd:true,
                queryapi: '/EQ_camera/query',    //数据请求路径
                addapi: '/EQ_camera/add',
                editapi: '/EQ_camera/edit',
                delapi: '/EQ_camera/remove',
                btswidth: '180',                 //按钮宽度
                fieldsstr: 'id__camera_name__ip__port__cusername__amanufacturer__worksite_id__passid__passname__limit_time__resume',//请求数据的格式，在云平台的页面找接口和有关请求参数。
                tableitems: [                       //表格元素，表头
                    /*{

                        hasSubs: false,
                        subs: [{
                            label: '编号',          //页面表格显示
                            prop: 'id',             //对应表中字段
                            width: '70',           //列宽度
                            type: 'number',         //对应表中字段类型
                            editable: false,         //是否可编辑
                            searchable: true,       //是否可查询
                            addable: false,          //是否可添加
                            unsortable: true,       //是否可排序
                            align: 'center'         //页面表格内容显示位置
                        }]
                    },*/
                    {

                        hasSubs: false,
                        subs: [{
                            label: '名称',          //页面表格显示
                            prop: 'camera_name',             //对应表中字段
                            width: '160',           //列宽度
                            type: 'str',         //对应表中字段类型
                            editable: true,         //是否可编辑
                            searchable: true,       //是否可查询
                            addable: true,          //是否可添加
                            unsortable: true,       //是否可排序
                            align: 'center'         //页面表格内容显示位置
                        }]
                    }, {

                        hasSubs: false,
                        subs: [{
                            label: 'IP地址',
                            prop: 'ip',
                            width: '130',
                            type: 'str',
                            editable: true,
                            searchable: true,
                            addable: true,
                            unsortable: true,
                            align: 'center'
                        }]
                    }, {

                        hasSubs: false,
                        subs: [{
                            label: '端口',
                            prop: 'port',
                            width: '100',
                            type: 'str',
                            editable: true,
                            searchable: true,
                            addable: true,
                            unsortable: true,
                            align: 'center'
                        }]
                    }, {

                        hasSubs: false,
                        subs: [{
                            label: '用户名',
                            prop: 'cusername',
                            width: '100',
                            type: 'str',
                            editable: true,
                            searchable: true,
                            addable: true,
                            unsortable: true,
                            align: 'center',
                        }]
                    }, {

                        hasSubs: false,
                        subs: [{
                            label: '制造商',
                            prop: 'manufacturer',
                            width: '150',
                            type: 'str',
                            editable: true,
                            searchable: true,
                            addable: true,
                            unsortable: true,
                            align: 'center',
                        }]
                    }, {

                        hasSubs: false,
                        subs: [{
                            label: '所属工作站',
                            prop: 'worksite_id',
                            width: '160',
                            type: 'selection',
                            selectlist:this.worksite_id,//引用通道管理的所属工作站数据
                            editable: true,
                            searchable: true,
                            addable: true,
                            unsortable: true,
                            align: 'center',
                            format:(row)=>{
                                return common.nameformat(row, this.worksite_id, 'worksite_id')
                            }
                        }]
                    },{

                        hasSubs: false,
                        subs: [{
                            label: '所属通道',
                            prop: 'passname',
                            width: '160',
                            type: 'selection',
                            selectlist:this.channelType,//引用通道管理的名称数据
                            editable: true,
                            searchable: true,
                            addable: true,
                            unsortable: true,
                            align: 'center',
                            format: (row) => {
                                return common.nameformat(row, this.channelType, 'passname')
                            }
                        }]
                    },
                ],

                addtitle: '添加摄像头',
                worksite_id:'',
                channelType:'',


            }
        },
        mounted() {
            window.onresize = () => {
                this.tableheight = common.gwh() - 143;
            };
            this.tableheight = common.gwh() - 143;

        },
        activated() {
            window.onresize = () => {
                this.tableheight = common.gwh() - 143;
            };
            this.tableheight = common.gwh() - 143;
            this.$refs['bolinkuniontable'].$refs['search'].resetSearch();
            this.$refs['bolinkuniontable'].getTableData({})

            let _this = this
            axios.all([common.getWorkSite_id()])
                .then(axios.spread(function (ret) {
                    _this.worksite_id = ret.data;
                    //console.log(ret.data);
                }))
            axios.all([common.getChannelType()])
                .then(axios.spread(function (ret) {
                    _this.channelType = ret.data;
                    //console.log(ret.data);
                }))
        },
        watch: {
            worksite_id: function (val) {
                this.tableitems[5].subs[0].selectlist = val;
                //console.log(val);
            },
            channelType: function (val) {
                this.tableitems[6].subs[0].selectlist = val;
                //console.log(val);
            }
        },
        methods: {}
    }
</script>

<style scoped>
    .gutter {
        display: none
    }
</style>