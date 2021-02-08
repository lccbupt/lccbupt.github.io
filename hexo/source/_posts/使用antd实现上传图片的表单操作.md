---
title: 使用antd实现上传图片的表单操作
date: 2020-06-23 18:29:41
tags: antd
---
### 一、antd介绍
antd 是基于 Ant Design 设计体系的 React UI 组件库，主要用于研发企业级中后台产品，有蚂蚁金服出品，本篇文章记录使用antd中upload组件与服务端配合实现文件的上传，本文中使用的antd版本是3.x版本。
官网：https://ant.design/index-cn
### 二、antd中使用upload、Form组件实现文件上传
1.具体功能
在一个服务记录的表单中，有一项是上传图片，需在保存表单的时候提交已经上传的图片地址
2.实现流程
- 点击上传按钮，将图片上传到服务端指定的api接口
- 服务端接口返回上传图片的地址链接
- 将图片的地址链接绑定到表单的对应项
- 点击保存按钮，提交表单

<!--more-->
{% asset_img 效果图.png 表单结构图 %}
3.具体代码实现
        // 代码有省略...
        import { Form, Upload } from 'antd';

        const Demo = () => {
            const { getFieldDecorator } = this.props.form;
            // 处理上传按钮每次触发事件
            const handleChange = info => {
                let filelist = info.fileList;
                const curFile = info.file;
                if (curFile.response && curFile.response.errno === 0) {
                  message.success('上传成功')
                }
                if (curFile.response && curFile.response.errno !== 0) {
                  message.error(curFile.response.errmsg)
                }
                // 向fileList中增加url字段来记录每次上传成功后服务端返回的图片地址
                filelist = filelist.map((file: { response: { errno: number; data: any; }; url: any; }) => {
                  if (file.response && file.response.errno === 0) {
                    file.url = file.response.data
                  } else {
                    file.url = 0
                  }
                  return file;
                });
              }
            const uploadImgConfig = {
                action: '/servicerecord/upload',
                name: 'service_img',
                withCredentials: true,
            }
            // 点击保存处理service_img项对应的fileList对象
            const saveServiceInfo: IFetchApi = async () => {
                formdata?.validateFields(async (err, values) => {
                  if (err) return;
                  const imageIprops = values?.service_img.map((item: { url: any; }) => item.url)
                  values.service_img = imageIprops

                  const [result] = await fetchSaveApi(values);
                  if (result.errno === 0) {
                    message.success('保存成功');
                    formdata.resetFields();
                  }
                });
              };
            return (
                <Form>
                    <Form.Item label="上传图片">
                        {form!.getFieldDecorator('service_img', {
                          valuePropName: 'fileList',
                          getValueFromEvent: e => {
                            if (Array.isArray(e)) {
                              return e;
                            }
                            return e && e.fileList;
                          },
                        })(
                          <Upload
                            {...uploadImgConfig}
                            listType="picture-card"
                            onChange={handleChange}
                          >
                            <div>
                              <Icon type="plus" />
                              <div className="ant-upload-text">上传</div>
                            </div>
                          </Upload>,
                        )}
                      </Form.Item>
                </Form>
            )
        } 

4.总结valuePropName & getValueFromEvent
valuePropName：子节点的值的属性，如上代码中，对应子节点Upload的属性fileList
getValueFromEvent：可以把 onChange 的参数（如 event）转化为控件的值，如上面代码中绑定子节点Upload的fileList属性后，每次onChange将子节点Upload的属性fileList转化为service_img表单项的值。
因此通过以上两个参数就可以实现表单内图片的上传功能。记得在提交表单时，将fileList处理成自己想要的数据格式。