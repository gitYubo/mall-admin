<template>
    <Card :dis-hover="true" :shadow="false" :bordered="false">
        <h4 slot="title">标签管理</h4>
        <Row >
            <Col span="20">
                <Button type="primary" ghost class="tools" @click="addTag">新增标签</Button>
                <Button type="default" class="tools" @click="handleBatchDel">批量删除</Button>
           </Col>
            <Col span="4" style="display: flex;align-items: center;">
                <Input search enter-button placeholder="请输入标签名称" v-model="qurey.tagName" class="tools" style="margin-left:5px;min-width:200px" @on-search="getTagList"/>
            </Col>
        </Row>

        <Table @on-selection-change="selection" border :loading="loading" :columns="columns" :data="tableData"> </Table>
        <!-- <footer>
            <Page :total="100" show-elevator size="small"/>
        </footer> -->

        <EditTag v-model="showTagModal" :data="formData" :title="showTagModalTitle" @success="getTagList"></EditTag>

        <Modal v-model="delTagModal" width="360" :styles="{top: '20px'}">
            <p slot="header" style="color:#f60;text-align:center">
                <Icon type="ios-information-circle"></Icon>
                <span>删除标签</span>
            </p>
            <div style="text-align:center">
                <p>删除后无法恢复，确认将这些标签删除？</p>
            </div>
            <div slot="footer">
                <Button type="error" size="large" long :loading="delTagModaLoading" @click="handleStartDel">永久删除</Button>
            </div>
        </Modal>
    </Card>
</template>

<script>
    import EditTag from '@/components/EditTag'
    export default {
        components: {
            EditTag
        },
        data () {
            return {
                model1:'',
                loading:false,
                columns: [
                    {
                        type: 'selection',
                        width: 50,
                        align: 'center'
                    },
                    {
                        title: '标签名称',
                        key: 'tagName',
                    },
                    {
                        title: '标签绑定商品数',
                        key: 'bindingNum',
                    },
                    {
                        title: '操作',
                        key: 'action',
                        fixed: 'right',
                        width: 160,
                        render: (h, params) => {
                            return h('div', [
                                h('Button', {
                                    props: {
                                        type: 'text',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.editTag(params.index)
                                        }
                                    }
                                }, '编辑'),
                                h('Button', {
                                    props: {
                                        type: 'text',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.handleDel(params.index)
                                        }
                                    }
                                }, '删除')
                            ]);
                        }
                    }
                ],
                tableData: [],
                tableMultiSelect:[],
                tableSelect:[],
                // 添加、编辑
                showTagModal: false,
                submitLoading: true,
                showTagModalTitle: '',
                formData: {},
                ruleValidate: {
                    tagName: [{ required: true, message: '必须填写', trigger: 'blur' } ]
                },
                // 删除
                delTagModal:false,
                delTagModaLoading: false,
                qurey: {
                    tagName: ''
                }
            }
        },
        methods: {
            async getTagList(e){
                console.log(e)
                this.loading = true
                let data = await this.$api.tagList(this.qurey)
                this.tableData = data.data
                this.loading = false
            },
            addTag(){
                this.showTagModalTitle = '新建标签'
                this.formData['tagId'] = ''
                this.showTagModal = true
            },
            editTag(index){
                this.showTagModalTitle = '编辑标签'
                this.formData['tagId'] = this.tableData[index].tagId
                this.formData['tagName'] = this.tableData[index].tagName
                this.showTagModal = true
            },
            handleBatchDel(){
                if(!this.tableMultiSelect.length){
                    return this.$Message.error('请至少选择一个标签');
                }
                if(!this.delTagModal){
                    this.delTagModal = true
                    this.isMulti = true
                }
            },
            handleDel(index){
                this.tableSelect = [this.tableData[index].tagId]
                this.delTagModal = true
                this.isMulti = false
            },
            async handleStartDel(){
                try{
                    this.delTagModaLoading = true
                    if(this.isMulti){
                        await this.$api.delTag({tagId: this.tableMultiSelect.join(',')})
                        this.tableMultiSelect = []
                    } else {
                        await this.$api.delTag({tagId: this.tableSelect.join(',')})
                    }
                    this.delTagModaLoading = this.delTagModal = false
                    this.getTagList()
                } catch(err){
                    this.delTagModaLoading = false
                }
            },
            selection(event){
                this.tableMultiSelect = []
                event.map(item =>{
                    this.tableMultiSelect.push(item.tagId)
                })
            }
        },
        created(){
            this.getTagList()
        }
    }
</script>

<style lang="less" scoped>
.tools{
    margin-bottom: 10px;
}
footer{

}
</style>
