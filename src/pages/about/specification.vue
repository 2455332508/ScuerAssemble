<template>
  <view class="container">
    <view class="back-button" @click="goBack">
      <uni-icons type="back" size="24" color="#000"></uni-icons>
    </view>
    <text class="title">川大集合吧 - 规范与原则</text>
    
    <view class="section">
      <text class="section-title">平台原则</text><br>
      <text class="paragraph">
        <text class="bold">第一，建设一个校园内的互助平台：</text>
        用户在平台发布二手、求助、交友、兼职、就业等校园生活相关信息，集友们提供热心的帮助。
        </text><br>
        <text class="paragraph"><text class="bold">第二，友善、严肃的交流：</text>
            乐于助人、尊重他人的观点、不攻击、不引战。
        </text><br>
      <text class="paragraph"> &nbsp &nbsp &nbsp 我们会毫不犹豫的请出不符合上述原则的用户，以构建优质的社区环境。</text>
    </view>
    
    <view class="section">
      <text class="section-title">平台声明</text>
      <text class="paragraph">平台不是一个通用论坛，平台仅定位于几个明确的校内互助话题。不在这几个话题范围内的内容不适合在平台发布。以下是典型的不符合平台主题的内容：</text>
      <view class="list">
        <text class="list-item">政治、经济、国际形势、社会热点相关话题；</text>
        <text class="list-item">对其他个体、组织实施网络暴力、网络报复；</text>
      </view>
    </view>
    
    <view class="section">
      <text class="section-title">平台规范</text>
      <text class="paragraph">本规范旨在中华人民共和国法律框架下建设一个互助、友善的社区，违背这一主旨的行为将会得到不同级别的惩罚。</text>
      
      <view class="subsection">
        <text class="subsection-title">一、违反国家相关法律和互联网信息管理相关条例</text>
        <view class="list">
          <text class="list-item">禁止发布违反国家法律法规的信息；</text>
          <text class="list-item">禁止发布违反互联网信息管理条例的信息；</text>
          <text class="list-item">禁止发布色情、暴力、侵犯隐私、煽动、造谣等信息；</text>
          <text class="list-item">禁止发布盗版电子书、电影、音乐等版权信息；</text>
          <text class="list-item">禁止发布VPN、SS、SSR、V2RAY等相关非法信息；</text>
          <text class="list-item">禁止发布筹款、众筹、高利贷等非法金融活动信息；</text>
          <text class="list-item">其他各类违法违规信息。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">二、违反学校相关政策、规定的信息</text>
        <view class="list">
          <text class="list-item">禁止发布违反学校政策的信息；</text>
          <text class="list-item">禁止发布代考、代跑、作弊、代课等信息；</text>
          <text class="list-item">禁止发布与学校防疫政策相违背的信息；</text>
          <text class="list-item">禁止发布学生卡、饭卡等其他校园证件转让、租借信息；</text>
          <text class="list-item">禁止发布煽动反对学校相关政策的信息；</text>
          <text class="list-item">其他各类有违学校政策的信息。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">三、热点舆情讨论不符合平台定位</text>
        <view class="list">
          <text class="list-item">禁止讨论国家、学校热点舆情信息、政治敏感信息等；</text>
          <text class="list-item">禁止以各种隐晦方式讨论舆情信息；</text>
          <text class="list-item">禁止重复提问、发帖以引起舆情讨论。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">四、网暴</text>
        <view class="list">
          <text class="list-item">禁止对个体、群体进行攻击性的评头论足，包含不限于：性别、性取向、地域、经济状态等攻击性内容；</text>
          <text class="list-item">禁止发布追星信息、攻击明星或粉丝团信息；</text>
          <text class="list-item">禁止挂人，禁止针对某用户、某老师、某学生、某组织的攻击性内容。对其他个体的不满，请以友好的语言进行建议，禁止在平台实施网络报复。</text>
          <text class="list-item">评论者对发帖者的观点可以理性讨论、反驳，但禁止发起恶意人身攻击；</text>
          <text class="list-item">禁止对他人发布的二手物品发布攻击性内容；</text>
          <text class="list-item">禁止钓鱼类提问。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">五、粗俗内容</text>
        <view class="list">
          <text class="list-item">禁止脏话、脏话缩写等侮辱他人的文字、图片、表情等。如sb、nmsl、nt等；</text>
          <text class="list-item">禁止恶俗的文字、图片，引起读者不适；</text>
          <text class="list-item">禁止约架；</text>
          <text class="list-item">禁止约炮及其暗语等各类一夜情信息，例如，找一个O，约个走肾的。</text>
          <text class="list-item">禁止没有自我介绍或用户没有社区知名度的情况下，发布约喝酒、约看电影、约聊天、约异性等各种轻浮的交友行为。如：约一个异性今晚一起玩；</text>
          <text class="list-item">禁止水贴，例如，什么时候可以放假啊。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">六、不合适的恋爱交友信息</text>
        <view class="list">
          <text class="list-item">用户发布征恋爱对象内容时，应有足够让他人了解你的个人介绍。不可仅对对方提出各种要求，无完善的自我介绍；</text>
          <text class="list-item">不可发布轻浮的交友内容，例如：找一个美女，找一个猛男，找一个m等。</text>
          <text class="list-item">语言存在对他人的攻击。请使用文明用语，例如"要求对方不胖"，可改为"期望对方身材匀称"。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">七、暴露隐私</text>
        <view class="list">
          <text class="list-item">发布失物招领学生卡信息时，对学号、姓名、照片、二维码等关键信息进行局部打码；</text>
          <text class="list-item">发布求助获得某人的联系方式，不能在评论区发出他人的私人联系方式，如微信、电话、QQ、私人电邮等；</text>
          <text class="list-item">禁止人肉搜索他人，发布他人信息；</text>
          <text class="list-item">禁止指名道姓评论某同学、老师等其他校园工作人员；</text>
          <text class="list-item">禁止发布侵犯老师名誉的选课咨询、选导师咨询内容。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">八、商业推广、牟利信息</text>
        <view class="list">
          <text class="list-item">禁止发布商家信息，或代替他人或机构发布营销、引流、兼职等各类信息；</text>
          <text class="list-item">禁止发布微信群、QQ群、微信QQ二维码、公众号、表白墙、任何二维码等各类推广信息；</text>
          <text class="list-item">禁止发布淘宝、拼多多、京东、美团等推广活动以及相关的优惠物品，砍价、拉新、助力、红包、投票、团购券等各类商家营销活动；</text>
          <text class="list-item">禁止发布可能为你带来佣金的任何信息；</text>
          <text class="list-item">禁止代机构发布家教信息，禁止发布收取信息费的家教信息，只能发布家长直接的需求或同学间免费转让家教；</text>
          <text class="list-item">禁止出售电子资源、网课等可多次盈利的虚拟商品；</text>
          <text class="list-item">禁止代购、代跑腿的供应者发布供应信息，只能由需求方发起需求，供应方不能在需求方的评论中留下联系方式；</text>
          <text class="list-item">禁止以二手的名义经营微商；</text>
          <text class="list-item">禁止为周边商家发布软性广告；</text>
          <text class="list-item">禁止发布问卷调查，发布研究类问卷调查请先获得平台管理员同意；</text>
          <text class="list-item">禁止在非招聘栏目发布招聘信息；</text>
          <text class="list-item">禁止在非招聘栏目发布兼职招募信息，校内机构或实验室除外。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">九、重复发帖</text>
        <view class="list">
          <text class="list-item">同一内容、相似内容，7天内不能重复发布，不好卖的二手物品不要一直发布；</text>
          <text class="list-item">不要在短时间内发布多个二手信息，多个二手物品请合并发布；</text>
          <text class="list-item">发布内容前，建议在手机便签app中打好草稿，因内容错误重复发布仍然视为重复发帖；</text>
          <text class="list-item">禁止以在他人帖子下方评论的方式变相重复发帖。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">十、昵称头像</text>
        <view class="list">
          <text class="list-item">禁止昵称、头像中含有广告推广；</text>
          <text class="list-item">禁止头像含有二维码；</text>
          <text class="list-item">禁止昵称或头像伪装平台管理员，或使用与平台管理员、推送公众号相似的昵称或头像；</text>
          <text class="list-item">禁止昵称、头像侮辱他人，恶意使用国家领导人或学校相关人员的姓名、存在误导的类似姓名或照片。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">十一、破坏产品生态</text>
        <view class="list">
          <text class="list-item">禁止宣泄个人情绪，向平台倾倒垃圾情绪。</text>
          <text class="list-item">禁止标题党、题文不符等，如，震惊，某某某竟然；</text>
          <text class="list-item">禁止杠精，鸡蛋里挑骨头，不管看到什么都要抬杠；</text>
          <text class="list-item">禁止哗众取宠的抖机灵发言，重复玩梗；</text>
          <text class="list-item">禁止刻意绕开社区规则的擦边球帖子；</text>
          <text class="list-item">禁止发布与平台的栏目主题不符的帖子；</text>
          <text class="list-item">勿评论发布时间超过30日的帖子，对他人造成打扰；</text>
          <text class="list-item">勿频繁举报同一内容，勿提交无根据举报。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">十二、社团发布活动</text>
        <view class="list">
          <text class="list-item">需要写明主办单位；</text>
          <text class="list-item">需要声明活动性质：公益活动、或商业赞助；</text>
          <text class="list-item">不得涉及赞助商名称，否则视为广告。</text>
          <text class="list-item">帖子内容建议以正式文本发布，避免营销文案。</text>
        </view>
      </view>
      
      <view class="subsection">
        <text class="subsection-title">十三、其他不适合在平台发布内容</text>
        <view class="list">
          <text class="list-item">禁止发布约酒、蹦迪、麻将、剧本杀等有潜在商业行为和人身安全危害的活动招募；</text>
          <text class="list-item">禁止发布美团、饿了么等外卖优惠券买卖，大量重复的低额优惠券造成垃圾信息；</text>
          <text class="list-item">根据"双减"政策相关法规，禁止发布中小学学科类家教信息。</text>
        </view>
      </view>
    </view>
    
    <view class="section">
      <text class="section-title">惩罚措施</text>
      <text class="paragraph">有以上违反规范的用户将受到不同等级的惩罚，惩罚等级包含：</text>
      <view class="list">
        <text class="list-item">禁言1天、7天、30天、1年、永久；</text>
        <text class="list-item">禁止访问；</text>
        <text class="list-item">被多人举报触发自动禁言机制；</text>
        <text class="list-item">账号处于惩罚期间，不支持更改身份认证。</text>
      </view>
    </view>
    
    <text class="updated-time">文档更新时间：2023-10-26</text>
  </view>
</template>

<script>
export default {
  name: 'Specification',
  data() {
    return {
      // 如果需要添加数据
    }
  },
  methods: {
    goBack() {
    //   uni.navigateBack({
    //     delta: 1
    //   })
    uni.redirectTo({
            url: '/pages/about/aboutUs'
          })
    }
  },
  mounted() {
    console.log('校园平台规范页面已加载')
  }
}
</script>

<style lang="scss">
    .container {
      background-color: #fff;
  border-radius: 16rpx;
  box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.1);
  padding: 60rpx;
  margin: 40rpx;
  position: relative;
  
  .back-button {
    position: absolute;
    top: 20rpx;
    left: 20rpx;
    padding: 20rpx;
    z-index: 100;
    
    &:active {
      opacity: 0.7;
    }
  }
  
  .title {
      color: #3a7bd5;
      text-align: center;
    margin-bottom: 60rpx;
    font-size: 56rpx;
    font-weight: bold;
    padding-top: 40rpx;
  }
  
  .section {
    margin-bottom: 40rpx;
    
    .section-title {
      color: #2c3e50;
      margin: 50rpx 0 30rpx;
      padding-bottom: 16rpx;
      border-bottom: 4rpx solid #ecf0f1;
      font-size: 44rpx;
      font-weight: bold;
    }
    
    .subsection-title {
      color: #34495e;
      margin: 30rpx 0 20rpx;
      font-size: 36rpx;
      font-weight: bold;
    }
    
    .paragraph {
      margin-bottom: 30rpx;
      font-size: 32rpx;
      line-height: 1.6;
      
      .bold {
        font-weight: bold;
      }
    }
    
    
    .list {
      margin-left: 40rpx;
      margin-bottom: 30rpx;
      
      .list-item {
        margin-bottom: 16rpx;
        font-size: 30rpx;
        line-height: 1.6;
      }
    }
  }
  
    .updated-time {
      text-align: right;
    font-size: 26rpx;
      color: #7f8c8d;
    margin-top: 60rpx;
      font-style: italic;
      }
    }
  </style>