---
title: Ant Design的Table组件实现点击行高亮提示
date: 2020-05-08 16:28:20
tags: Antd
---
### 一、Ant Design简介
> antd 是蚂蚁金服出品的面向企业级的设计体系组件库，使用react封装，在后台项目中使用体验很好；
官网地址：https://ant.design/index-cn

### 二、需求
使用antd中的Table组件展示接口返回的表格数据；并且当点击表格的某一行时，需要当前行高亮显示

### 三、解决方法
主要使用Table 组件的rowClassName 和 onRow 属性；
- rowClassName 表示行的类名
- onRow 设置行属性
<!--more-->

具体实现代码如下：

        import React, { useState } from 'react';
        import { Table } from 'antd'

        const Demo = () => {
            const [rowid, setRowId] = useState();

            // 设置行的class类名
            const setRowClassName = record => {
                return record === rowid ? 'active' : '';
            }

            // 设置行的点击事件
            const changeRow = record => {
                setRowId(record.id) 
            }

            return (
                <Table 
                    onRow={ record => {
                        return {
                            onClick: () => { changeRow(record) }
                        };
                    }}
                    rowClassName={setRowClassName}
                />
            )
        }

上述代码通过点击行事件，为该行添加active类名，从而修改改行的样式，比如添加高亮背景。