<template>

    <view class="back-button" @click="goBack">
      <uni-icons type="back" size="60" color="#000"></uni-icons>
    </view>
    <br>
    <br>

    <div class="privacy-policy-container">
      <!-- 头部 -->
      <header class="header">
        <h1>APP隐私保护指引</h1>
        <p class="version">版本 3.0 | 更新日期：2025年3月30日</p>
      </header>
  
      <!-- 主体内容 -->
      <main class="content">
        <view v-for="(section, index) in sections" :key="index" class="section">
          <view class="section-title" @click="toggleSection(index)">
            {{ section.title }}
            <text class="arrow">{{ section.isOpen ? '▼' : '▶' }}</text>
          </view>
          <view v-show="section.isOpen" class="section-body">
            <view class="section-content" v-html="section.content"></view>
          </view>
        </view>
      </main>
  
      <!-- 页脚 -->
      <footer class="footer">
        <p>© 2025 川大集合吧APP 版权所有</p>
        <p>生效日期：2025年3月30日</p>
      </footer>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  
  const goBack = () => {
    uni.redirectTo({
      url: '/pages/about/aboutUs'
    })
  }
  
  const sections = ref([
    {
      title: '一、我们如何收集您的个人信息',
      isOpen: false,
      content: `
        <p>我们会遵循正当、合法、必要的原则，出于本指引所述的以下目的，收集和使用您在使用服务过程中主动提供或因使用校园平台产品和/或服务而产生的个人信息。</p>
        
        <h3>收集原则</h3>
        <p>如果我们要将您的个人信息用于本指引未载明的其它用途，或基于特定目的将收集而来的信息用于其他目的，我们将以合理的方式向您告知，并再次征得您的同意。</p>
  
        <h3>账号相关</h3>
        <ul>
          <li>微信/QQ第三方登录：获取ID、昵称、头像</li>
          <li>实名认证：需提供手机号或校园身份信息</li>
        </ul>
  
        <h3>功能使用</h3>
        <ul>
          <li>发帖/评论：收集网络日志和发布内容</li>
          <li>身份认证：需提供学校、学号等信息</li>
          <li>消费记录：记录交易信息及支付方式</li>
        </ul>
      `
    },
    {
      title: '二、我们如何委托处理、共享、转让、公开披露您的个人信息',
      isOpen: false,
      content: `
        <p>在提供相对基本的业务功能以外，我们一直在努力提供更丰富的平台产品和/服务，以扩展、提高和加强您的使用体验。</p>
      `
    },
    {
      title: '三、我们如何保护您的个人信息',
      isOpen: false,
      content: `
        <p>我们会采取合理可行的措施，尽力保障您个人信息的安全。</p>
      `
    },
    {
      title: '四、我们如何管理您的个人信息',
      isOpen: false,
      content: `
        <p>您可以通过本指引中提供的联系方式与我们联系，行使您的相关权利。</p>
      `
    },
    {
      title: '五、我们如何存储您的个人信息',
      isOpen: false,
      content: `
        <p>我们会按照法律法规规定，将境内收集的用户个人信息存储在境内。</p>
      `
    },
    {
      title: '六、Cookie 和其他技术',
      isOpen: false,
      content: `
        <p>我们使用Cookie和类似技术来提供更好的用户体验。</p>
      `
    },
    {
      title: '七、我们如何更新与修改本指引',
      isOpen: false,
      content: `
        <p>我们会不时对指引进行修订。当发生实质变更时，我们会在版本更新后以推送通知、弹窗等形式向您展示变更后的内容。</p>
      `
    },
    {
      title: '八、我们如何处理未成年人的个人信息',
      isOpen: false,
      content: `
        <p>我们建议未成年人在监护人的指导下使用我们的服务。</p>
      `
    },
    {
      title: '九、您如何联系我们',
      isOpen: false,
      content: `
        <div class="contact-info">
          <p>四川大学</p>
          <p>四川大学</p>
          <p>联系方式：请通过APP内客服系统联系</p>
        </div>
      `
    },
    {
      title: '十、争议解决',
      isOpen: false,
      content: `
        <p>本指引的订立、执行和解释及争议的解决均应适用中华人民共和国法律。</p>
      `
    },
    {
      title: '十一、概念及定义',
      isOpen: false,
      content: `
        <p>本指引中使用的特定词语，具有如下含义：</p>
        <ul>
          <li>个人信息：指以电子或者其他方式记录的与已识别或者可识别的自然人有关的各种信息。</li>
          <li>个人敏感信息：指一旦泄露、非法提供或滥用可能危害人身和财产安全，极易导致个人名誉、身心健康受到损害或歧视性待遇等的个人信息。</li>
        </ul>
      `
    }
  ])

  const toggleSection = (index) => {
    sections.value[index].isOpen = !sections.value[index].isOpen
  }
  </script>
  
  <style scoped>
  .privacy-policy-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
    font-family: 'Segoe UI', system-ui, sans-serif;
    line-height: 1.8;
  }
  
  .header {
    text-align: center;
    padding: 2rem 0;
    border-bottom: 2px solid #eee;
    margin-bottom: 2rem;
  }
  
  .header h1 {
    color: #2c3e50;
    margin-bottom: 0.5rem;
  }
  
  .version {
    color: #666;
    font-size: 0.9em;
  }
  
  .section {
    margin-bottom: 1.5rem;
    border: 1px solid #eee;
    border-radius: 8px;
    overflow: hidden;
  }
  
  .section-title {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem;
    background: #f8f9fa;
    border: none;
    cursor: pointer;
    text-align: left;
    font-size: 1.1em;
    font-weight: 600;
    color: #2c3e50;
    transition: all 0.3s ease;
  }
  
  .section-title:hover {
    background: #f1f3f5;
  }
  
  .arrow {
    font-size: 0.8em;
    transition: transform 0.3s ease;
  }
  
  .section-body {
    padding: 1.5rem;
    background: white;
  }
  
  .section-content {
    color: #2c3e50;
  }
  
  .section-content h3 {
    color: #3498db;
    margin: 1.5rem 0 1rem;
  }
  
  .section-content p {
    margin-bottom: 1rem;
  }
  
  .section-content ul {
    padding-left: 1.5rem;
    list-style-type: disc;
    margin-bottom: 1rem;
  }
  
  .contact-info {
    background: #f8f9fa;
    padding: 1.5rem;
    border-radius: 8px;
  }
  
  .footer {
    text-align: center;
    padding: 2rem 0;
    border-top: 2px solid #eee;
    margin-top: 2rem;
    color: #666;
    font-size: 0.9em;
  }
  
  @media (max-width: 768px) {
    .privacy-policy-container {
      padding: 1rem;
    }
    
    .section-title {
      font-size: 1em;
    }
  }
  </style>