<template>
  <a-modal
    :title="title"
    :width="width"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="科目编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'courseNo', validatorRules.courseNo]" placeholder="请输入科目编号"></a-input>
        </a-form-item>
        <a-form-item label="教室考场号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'examNo', validatorRules.examNo]" placeholder="请输入教室考场号"></a-input>
        </a-form-item>
        <a-form-item label="考试批次号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'batchNo', validatorRules.batchNo]" placeholder="请输入考试批次号"></a-input>
        </a-form-item>
        <a-form-item label="教师编号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'teacherNo', validatorRules.teacherNo]" placeholder="请输入教师编号"></a-input>
        </a-form-item>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: "ExamArrangeModal",
    components: { 
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
          courseNo: {rules: [
            {required: true, message: '请输入科目编号!'},
          ]},
          examNo: {rules: [
            {required: true, message: '请输入教室考场号!'},
          ]},
          batchNo: {rules: [
            {required: true, message: '请输入考试批次号!'},
          ]},
          teacherNo: {rules: [
            {required: true, message: '请输入教师编号!'},
          ]},
        },
        url: {
          add: "/exam/examArrange/add",
          edit: "/exam/examArrange/edit",
        }
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'courseNo','examNo','batchNo','teacherNo'))
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            let formData = Object.assign(this.model, values);
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }
         
        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'courseNo','examNo','batchNo','teacherNo'))
      },

      
    }
  }
</script>