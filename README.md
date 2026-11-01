# YOLO 与 DeepSeek/Qwen 大语言模型花卉识别检测系统   | 花卉识别 YOLO DeepSeek Qwen 目标检测 智慧教育9196期

## 系统概述

本系统基于 **YOLO 目标检测模型**与 **DeepSeek/Qwen 大语言模型**，融合 **Vue3、SpringBoot、Flask、Python** 等技术，构建了一套完整的**花卉目标识别与科普分析平台**。系统支持图片、批量图片、视频、摄像头实时检测，适配科普教育、植物研究及智能识别等场景。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a78269a6e5fc4870aebc34d0ab683d18.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/16eae1e4faa84504828bb11cf44e88be.png)

## 系统核心信息

| 项目 | 详细说明 |
| :--- | :--- |
| **检测类别** | 涵盖多种常见花卉类别（具体类别以模型权重为准） |
| **核心技术** | YOLO + DeepSeek/Qwen + Flask + SpringBoot + Vue3 |
| **检测模式** | 图片检测、批量图片检测、视频检测、摄像头实时检测 |
| **系统架构** | 前后端分离（Vue3 + SpringBoot + Flask） |
| **应用价值** | 1. **科普教育**：支撑花卉的自动识别与科普介绍，辅助植物学学习。<br>2. **植物研究**：为花卉分类、形态分析及生态调查提供视觉工具。<br>3. **智能识别**：可迁移至其他植物或物体识别场景，支持自定义训练。<br>4. **AI 建议**：结合大语言模型生成花卉介绍、形态特征、生长习性、常见用途及养护建议。 |

## 技术栈

| 层次 | 技术选型 |
| :--- | :--- |
| **前端** | Vue3、Vite、Element Plus、Pinia、Vue Router、ECharts |
| **业务后端** | Spring Boot |
| **算法服务** | Python、Flask、Flask-SocketIO |
| **算法** | YOLO 目标检测 |
| **AI 接入** | DeepSeek、Qwen |
| **标注格式** | YOLO txt |
| **其他** | OpenCV、Socket 实时进度推送、前后端分离架构 |


![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/25163f0564f84c8d828360c12b1b437a.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4bbfc3436e5342539b80803d1684f439.png)

## 系统功能

**1. 用户管理**
- 用户登录、注册
- 个人中心：查看与修改个人信息
- 用户管理后台（管理员）

**2. 多模态检测支持**
- **图片花卉识别检测**：单张图片上传识别
- **批量图片花卉检测**：批量上传，自动处理与结果导出
- **视频花卉识别检测**：上传视频，逐帧分析并输出标注视频
- **摄像头实时花卉检测**：连接摄像头，实时输出检测结果

**3. 检测结果可视化**
- 检测结果可视化展示
- 花卉类别与置信度展示
- 检测耗时与结果图像预览
- 图片/视频/摄像头识别记录保存与查询
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/198ee9a6296846e1818cc1eef0fef21a.png)

**4. AI 智能建议**
- 结合 DeepSeek / Qwen，根据检测结果生成花卉介绍、形态特征、生长习性、常见用途和养护建议
- 提供科普与养护分析报告

**5. 模型管理**
- 支持 YOLO 权重模型切换
- 支持置信度阈值调整
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5bb605388b264e29b08da23bdc75b104.png)

## 交付内容

1. **系统源码 + 部署教程**
2. **训练好的模型和数据集**
3. **简要的项目文档**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e511d4ff33de4986b6b357d4f3bf7739.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/34cff8bf59be4147b3e78d6d3b7d81a6.png)

## 快速开始（代码示例）

**1. 数据集配置文件 (`data.yaml`)**
```yaml
train: ./dataset/images/train
val: ./dataset/images/val
nc: 5
names: ['玫瑰', '郁金香', '向日葵', '百合', '兰花']
```

**2. YOLO 训练代码**
```python
from ultralytics import YOLO

model = YOLO('yolov8m.pt')
model.train(
    data='data.yaml',
    epochs=100,
    imgsz=640,
    batch=16,
    device=0
)
```

**3. Flask 推理与 AI 分析服务**
```python
from flask import Flask, request, jsonify
from ultralytics import YOLO
import cv2
import numpy as np

app = Flask(__name__)
model = YOLO('runs/detect/train/weights/best.pt')

@app.route('/detect', methods=['POST'])
def detect():
    file = request.files['image']
    img = cv2.imdecode(np.frombuffer(file.read(), np.uint8), cv2.IMREAD_COLOR)
    results = model(img)
    detections = []
    for box in results[0].boxes:
        detections.append({
            'class': model.names[int(box.cls[0])],
            'confidence': float(box.conf[0]),
            'bbox': box.xyxy[0].tolist()
        })
    # 调用 DeepSeek/Qwen 生成花卉科普与养护建议
    ai_suggestion = analyze_with_llm(detections)
    return jsonify({'detections': detections, 'suggestion': ai_suggestion})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

**4. Vue3 前端检测页面**
```vue
<template>
  <div class="detector-container">
    <el-upload action="/api/detection/image" :on-success="handleSuccess">
      <el-button type="primary">上传图片</el-button>
    </el-upload>
    <div v-if="resultImage" class="result-area">
      <img :src="resultImage" alt="Detection Result" />
      <el-table :data="detections" border>
        <el-table-column prop="class" label="花卉类别" />
        <el-table-column prop="confidence" label="置信度" />
      </el-table>
      <el-button @click="generateAISuggestion">生成AI科普建议</el-button>
    </div>
  </div>
</template>
```

## 标签
`花卉识别` `YOLO` `DeepSeek` `Qwen` `目标检测` `智慧教育` `深度学习` `计算机视觉`
