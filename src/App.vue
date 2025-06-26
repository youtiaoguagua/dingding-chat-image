<template>
  <div class="chat-generator">
    <div class="container">
      <!-- 控制面板 -->
      <div class="control-panel">
        <h2>聊天设置</h2>
        
        <el-collapse v-model="activeCollapse">
          <!-- 颜色设置 -->
          <el-collapse-item title="颜色设置" name="colors">
            <div class="color-settings">
              <div class="color-item">
                <label>背景颜色</label>
                <div class="color-input">
                  <el-color-picker v-model="settings.bgColor" @change="updatePreview" />
                  <el-input v-model="settings.bgColorText" @input="syncColorInput" placeholder="#f0f2f5" />
                </div>
              </div>
              
              <div class="color-item">
                <label>气泡颜色</label>
                <div class="color-input">
                  <el-color-picker v-model="settings.bubbleColor" @change="updatePreview" />
                  <el-input v-model="settings.bubbleColorText" @input="syncColorInput" placeholder="#ffffff" />
                </div>
              </div>
              
              <div class="color-item">
                <label>文字颜色</label>
                <div class="color-input">
                  <el-color-picker v-model="settings.textColor" @change="updatePreview" />
                  <el-input v-model="settings.textColorText" @input="syncColorInput" placeholder="#000" />
                </div>
              </div>
            </div>
          </el-collapse-item>

          <!-- 群名称设置 -->
          <el-collapse-item title="群名称设置" name="group">
            <el-input v-model="settings.groupName" placeholder="请输入群名称" @input="updatePreview" />
            <el-checkbox v-model="settings.showGroupName" @change="updatePreview" style="margin-top: 12px;">
              显示群名称
            </el-checkbox>
          </el-collapse-item>

          <!-- 头像设置 -->
          <el-collapse-item title="头像设置" name="avatar">
            <el-tabs v-model="activeAvatarTab" @tab-change="handleAvatarTabChange">
              <el-tab-pane label="他人头像" name="other">
                <div class="avatar-upload">
                  <el-upload
                    class="avatar-uploader"
                    :auto-upload="false"
                    :show-file-list="false"
                    accept="image/*"
                    @change="handleOtherAvatarChange"
                  >
                    <img v-if="settings.otherAvatarUrl" :src="settings.otherAvatarUrl" class="avatar" />
                    <el-icon v-else class="avatar-uploader-icon"><Plus /></el-icon>
                  </el-upload>
                  <el-button 
                    v-if="settings.otherAvatarUrl"
                    type="danger" 
                    size="small" 
                    @click="deleteOtherAvatar"
                    class="delete-avatar-btn"
                  >
                    <el-icon><Delete /></el-icon>
                    删除头像
                  </el-button>
                </div>
              </el-tab-pane>
              
              <el-tab-pane label="我的头像" name="self">
                <div class="avatar-upload">
                  <el-upload
                    class="avatar-uploader"
                    :auto-upload="false"
                    :show-file-list="false"
                    accept="image/*"
                    @change="handleSelfAvatarChange"
                  >
                    <img v-if="settings.selfAvatarUrl" :src="settings.selfAvatarUrl" class="avatar" />
                    <el-icon v-else class="avatar-uploader-icon"><Plus /></el-icon>
                  </el-upload>
                  <el-button 
                    v-if="settings.selfAvatarUrl"
                    type="danger" 
                    size="small" 
                    @click="deleteSelfAvatar"
                    class="delete-avatar-btn"
                  >
                    <el-icon><Delete /></el-icon>
                    删除头像
                  </el-button>
                </div>
              </el-tab-pane>
            </el-tabs>
          </el-collapse-item>

          <!-- 对话管理 -->
          <el-collapse-item title="对话管理" name="messages">
            <div class="message-list">
              <div 
                v-for="(message, index) in messages" 
                :key="index"
                class="message-item"
                :class="{ active: currentMessageIndex === index }"
                @click="selectMessage(index)"
              >
                <div class="message-preview">
                  <strong>{{ message.username }}</strong> ({{ message.isSelf ? '我' : '他人' }}): 
                  {{ message.message.substring(0, 30) }}{{ message.message.length > 30 ? '...' : '' }}
                </div>
                <div class="message-actions">
                  <el-button size="small" @click.stop="moveMessage(index, -1)" :disabled="index === 0">
                    <el-icon><ArrowUp /></el-icon>
                  </el-button>
                  <el-button size="small" @click.stop="moveMessage(index, 1)" :disabled="index === messages.length - 1">
                    <el-icon><ArrowDown /></el-icon>
                  </el-button>
                  <el-button size="small" type="primary" @click.stop="editMessage(index)">
                    <el-icon><Edit /></el-icon>
                  </el-button>
                  <el-button size="small" type="danger" @click.stop="deleteMessage(index)">
                    <el-icon><Delete /></el-icon>
                  </el-button>
                </div>
              </div>
            </div>
            
            <el-button type="success" @click="openAddMessageModal" style="width: 100%; margin-top: 12px;">
              添加新消息
            </el-button>
          </el-collapse-item>
        </el-collapse>

        <!-- 操作按钮 -->
        <div class="action-buttons">
          <el-button type="primary" @click="updatePreview">
            更新预览
          </el-button>
          <el-button type="success" @click="downloadScreenshot">
            下载截图
          </el-button>
          <el-button @click="setAweiAvatar">
            一键阿伟
          </el-button>
        </div>
      </div>

      <!-- 预览面板 -->
      <div class="preview-panel">
        <h2>预览效果</h2>
        <div class="chat-preview" ref="chatPreview" :style="{ backgroundColor: settings.bgColor }">
          <div v-if="messages.length === 0" class="empty-state">
            <el-icon size="40" color="#888"><ChatDotRound /></el-icon>
            <p>点击"添加消息"开始创建对话</p>
          </div>
          <div v-else>
            <div v-if="settings.showGroupName && settings.groupName" class="group-header">
              {{ settings.groupName }}
            </div>
            <div class="chat-messages-container">
              <div 
                v-for="(message, index) in messages" 
                :key="index"
                class="chat-message"
                :class="{ self: message.isSelf }"
              >
                <div class="avatar">
                  <img v-if="getAvatarUrl(message)" :src="getAvatarUrl(message)" :alt="message.username" />
                  <div 
                    v-else 
                    class="default-avatar"
                    :style="{
                      backgroundColor: message.textAvatarBgColor || '#007AFF',
                      color: message.textAvatarTextColor || '#ffffff'
                    }"
                  >
                    {{ splitUsername(message.username, message.customText) }}
                  </div>
                </div>
                <div class="message-content">
                  <div class="username">{{ message.username }}</div>
                  <div 
                    class="message-bubble"
                    :style="{ 
                      backgroundColor: message.isSelf ? '#007AFF' : settings.bubbleColor,
                      color: message.isSelf ? '#ffffff' : settings.textColor
                    }"
                  >
                    <div class="message-text">{{ message.message }}</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- 添加/编辑消息对话框 -->
    <el-dialog
      v-model="messageDialogVisible"
      :title="isEditing ? '编辑消息' : '添加新消息'"
      width="400px"
      @close="closeMessageDialog"
    >
      <el-form :model="newMessage" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="newMessage.username" placeholder="用户名" />
        </el-form-item>
        <el-form-item label="消息内容">
          <el-input 
            v-model="newMessage.message" 
            type="textarea" 
            :rows="3"
            placeholder="消息内容" 
          />
        </el-form-item>
        <el-form-item label="消息类型">
          <el-select v-model="newMessage.isSelf" style="width: 100%;">
            <el-option label="我的消息" :value="true" />
            <el-option label="他人消息" :value="false" />
          </el-select>
        </el-form-item>
        <el-form-item label="头像设置">
          <div class="avatar-setting-container">
            <el-radio-group v-model="newMessage.avatarType" @change="updateAvatarPreview" class="avatar-radio-group">
              <el-radio-button label="default">文字头像</el-radio-button>
              <el-radio-button label="custom">上传头像</el-radio-button>
              <el-radio-button label="none" :disabled="!hasDefaultAvatar">默认头像</el-radio-button>
            </el-radio-group>
            
            <!-- 头像预览区域 -->
            <div class="avatar-preview-section">
              <div class="avatar-preview-container">
                <el-avatar 
                  :size="50" 
                  v-if="newMessage.avatarType === 'none' && getAvatarUrl({ isSelf: newMessage.isSelf, avatarUrl: null })"
                  :src="getAvatarUrl({ isSelf: newMessage.isSelf, avatarUrl: null })"
                >
                  <el-icon><User /></el-icon>
                </el-avatar>
                <el-avatar 
                  :size="50" 
                  v-else-if="newMessage.avatarType === 'default'"
                  :style="{ 
                    backgroundColor: newMessage.textAvatarBgColor, 
                    color: newMessage.textAvatarTextColor, 
                    fontSize: '16px', 
                    fontWeight: 'bold' 
                  }"
                >
                  {{ getAvatarPreviewText() }}
                </el-avatar>
                <el-avatar 
                  :size="50" 
                  v-else-if="newMessage.avatarType === 'custom' && newMessage.avatarUrl"
                  :src="newMessage.avatarUrl"
                >
                  <el-icon><User /></el-icon>
                </el-avatar>
                <el-avatar 
                  :size="50" 
                  v-else
                  :style="{ backgroundColor: '#f0f0f0', color: '#999' }"
                >
                  <el-icon><Plus /></el-icon>
                </el-avatar>
              </div>
              <div class="avatar-preview-label">{{ getAvatarTypeLabel() }}</div>
            </div>
          </div>
          
          <!-- 文字头像自定义输入 -->
          <el-collapse-transition>
            <div v-if="newMessage.avatarType === 'default'" class="avatar-custom-input">
              <el-input 
                v-model="newMessage.customText" 
                placeholder="自定义文字（留空则使用用户名）" 
                maxlength="4"
                show-word-limit
                size="small"
              />
              <div class="input-tip">
                <el-icon><InfoFilled /></el-icon>
                最多4个字符，留空则自动使用用户名后两位
              </div>
              
              <!-- 文字头像颜色设置 -->
              <div class="text-avatar-colors">
                <div class="color-row">
                  <div class="color-item">
                    <label>背景颜色</label>
                    <div class="color-input-group">
                      <el-color-picker 
                        v-model="newMessage.textAvatarBgColor" 
                        size="small"
                        @change="updateAvatarPreview"
                      />
                      <el-input 
                        v-model="newMessage.textAvatarBgColor" 
                        size="small"
                        placeholder="#007AFF"
                        @input="updateAvatarPreview"
                      />
                    </div>
                  </div>
                  <div class="color-item">
                    <label>文字颜色</label>
                    <div class="color-input-group">
                      <el-color-picker 
                        v-model="newMessage.textAvatarTextColor" 
                        size="small"
                        @change="updateAvatarPreview"
                      />
                      <el-input 
                        v-model="newMessage.textAvatarTextColor" 
                        size="small"
                        placeholder="#ffffff"
                        @input="updateAvatarPreview"
                      />
                    </div>
                  </div>
                </div>
                <div class="color-presets">
                  <span class="preset-label">快速选择：</span>
                  <div class="preset-colors">
                    <div 
                      v-for="preset in colorPresets" 
                      :key="preset.name"
                      class="preset-color"
                      :style="{ backgroundColor: preset.bg, color: preset.text }"
                      @click="applyColorPreset(preset)"
                      :title="preset.name"
                    >
                      {{ getAvatarPreviewText().slice(0, 1) }}
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </el-collapse-transition>
          
          <!-- 上传头像区域 -->
          <el-collapse-transition>
            <div v-if="newMessage.avatarType === 'custom'" class="avatar-upload-section">
              <el-upload
                class="custom-avatar-uploader"
                :auto-upload="false"
                :show-file-list="false"
                accept="image/*"
                @change="handleCustomAvatarChange"
                drag
              >
                <div v-if="!newMessage.avatarUrl" class="upload-area">
                  <el-icon class="upload-icon"><UploadFilled /></el-icon>
                  <div class="upload-text">点击或拖拽上传头像</div>
                  <div class="upload-hint">支持 jpg、png 格式，建议尺寸 1:1</div>
                </div>
                <div v-else class="uploaded-image">
                  <img :src="newMessage.avatarUrl" alt="上传的头像" />
                  <div class="image-overlay">
                    <el-icon><Edit /></el-icon>
                    <span>点击重新上传</span>
                  </div>
                </div>
              </el-upload>
              <el-button 
                v-if="newMessage.avatarUrl"
                type="danger" 
                size="small" 
                @click="deleteCustomAvatar"
                class="delete-uploaded-avatar"
                plain
              >
                <el-icon><Delete /></el-icon>
                删除头像
              </el-button>
            </div>
          </el-collapse-transition>
          
          <!-- 默认头像提示 -->
          <el-collapse-transition>
            <div v-if="newMessage.avatarType === 'none'" class="default-avatar-tip">
              <el-alert
                v-if="!hasDefaultAvatar"
                title="暂无默认头像"
                type="warning"
                :closable="false"
                show-icon
              >
                <template #default>
                  请先在左侧"头像设置"中上传{{ newMessage.isSelf ? '我的头像' : '他人头像' }}
                </template>
              </el-alert>
              <el-alert
                v-else
                title="将使用默认头像"
                type="info"
                :closable="false"
                show-icon
              >
                <template #default>
                  使用左侧设置的{{ newMessage.isSelf ? '我的头像' : '他人头像' }}
                </template>
              </el-alert>
            </div>
          </el-collapse-transition>
        </el-form-item>
      </el-form>
      
      <template #footer>
        <el-button @click="closeMessageDialog">取消</el-button>
        <el-button type="primary" @click="saveMessage" style="margin-left: 12px;">确定</el-button>
      </template>
    </el-dialog>

    <!-- 底部标识 -->
    <div class="footer">
      Powered by <a href="https://youtiaoguagua.com" target="_blank">youtiaoguagua</a>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, computed, nextTick } from 'vue'
import { ElMessage } from 'element-plus'
import { 
  Plus, 
  Delete, 
  User, 
  ChatDotRound, 
  Edit, 
  UploadFilled, 
  InfoFilled 
} from '@element-plus/icons-vue'
import html2canvas from 'html2canvas'

// 响应式数据
const settings = reactive({
  bgColor: '#f0f2f5',
  bgColorText: '#f0f2f5',
  bubbleColor: '#ffffff',
  bubbleColorText: '#ffffff',
  textColor: '#000000',
  textColorText: '#000000',
  groupName: '技术交流群',
  showGroupName: true,
  otherAvatarUrl: 'https://img10.360buyimg.com/ddimg/jfs/t1/305052/18/13594/23198/685ca443F3774d9ab/967d365cbe09ae76.jpg',
  selfAvatarUrl: null
})

const messages = ref([])
const currentMessageIndex = ref(-1)
const activeAvatarTab = ref('other')
const activeCollapse = ref([]) // 默认展开颜色设置
const messageDialogVisible = ref(false)
const isEditing = ref(false)
const editingIndex = ref(-1)

const newMessage = reactive({
  username: '',
  message: '',
  isSelf: false,
  avatarType: 'default',
  avatarUrl: null,
  customText: '', // 自定义文字头像
  textAvatarBgColor: '#007AFF', // 文字头像背景颜色
  textAvatarTextColor: '#ffffff' // 文字头像文字颜色
})

const chatPreview = ref(null)

// 文字头像颜色预设
const colorPresets = [
  { name: '经典蓝', bg: '#007AFF', text: '#ffffff' },
  { name: '活力橙', bg: '#FF6B35', text: '#ffffff' },
  { name: '清新绿', bg: '#28A745', text: '#ffffff' },
  { name: '温暖红', bg: '#DC3545', text: '#ffffff' },
  { name: '优雅紫', bg: '#6F42C1', text: '#ffffff' },
  { name: '阳光黄', bg: '#FFC107', text: '#212529' },
  { name: '天空蓝', bg: '#17A2B8', text: '#ffffff' },
  { name: '玫瑰粉', bg: '#E83E8C', text: '#ffffff' },
  { name: '深灰', bg: '#6C757D', text: '#ffffff' },
  { name: '墨绿', bg: '#20C997', text: '#ffffff' }
]

// 计算属性
const hasDefaultAvatar = computed(() => {
  const isSelf = newMessage.isSelf
  return isSelf ? !!settings.selfAvatarUrl : !!settings.otherAvatarUrl
})

// 方法
const syncColorInput = () => {
  if (/^#[0-9A-F]{6}$/i.test(settings.bgColorText)) {
    settings.bgColor = settings.bgColorText
    updatePreview()
  }
}

const handleAvatarTabChange = (tabName) => {
  activeAvatarTab.value = tabName
}

const handleOtherAvatarChange = (file) => {
  const reader = new FileReader()
  reader.onload = (e) => {
    settings.otherAvatarUrl = e.target.result
    updatePreview()
  }
  reader.readAsDataURL(file.raw)
}

const handleSelfAvatarChange = (file) => {
  const reader = new FileReader()
  reader.onload = (e) => {
    settings.selfAvatarUrl = e.target.result
    updatePreview()
  }
  reader.readAsDataURL(file.raw)
}

const deleteOtherAvatar = () => {
  settings.otherAvatarUrl = null
  updatePreview()
}

const deleteSelfAvatar = () => {
  settings.selfAvatarUrl = null
  updatePreview()
}

const selectMessage = (index) => {
  currentMessageIndex.value = index
}

const moveMessage = (index, direction) => {
  if (index + direction < 0 || index + direction >= messages.value.length) return
  
  const temp = messages.value[index]
  messages.value[index] = messages.value[index + direction]
  messages.value[index + direction] = temp
  
  updatePreview()
}

const deleteMessage = (index) => {
  messages.value.splice(index, 1)
  if (currentMessageIndex.value >= messages.value.length) {
    currentMessageIndex.value = messages.value.length - 1
  }
  updatePreview()
}

const editMessage = (index) => {
  const msg = messages.value[index]
  newMessage.username = msg.username
  newMessage.message = msg.message
  newMessage.isSelf = msg.isSelf
  
  if (msg.avatarUrl === null) {
    newMessage.avatarType = 'none'
  } else if (msg.avatarUrl === 'default' || !msg.avatarUrl) {
    newMessage.avatarType = 'default'
    newMessage.customText = msg.customText || ''
    newMessage.textAvatarBgColor = msg.textAvatarBgColor || '#007AFF'
    newMessage.textAvatarTextColor = msg.textAvatarTextColor || '#ffffff'
  } else {
    newMessage.avatarType = 'custom'
    newMessage.avatarUrl = msg.avatarUrl
  }
  
  isEditing.value = true
  editingIndex.value = index
  messageDialogVisible.value = true
}

const openAddMessageModal = () => {
  newMessage.username = '赵津伟（迦勒）'
  newMessage.message = '我也认同这个观点'
  newMessage.isSelf = false
  newMessage.avatarType = 'default'
  newMessage.avatarUrl = null
  newMessage.customText = ''
  newMessage.textAvatarBgColor = '#007AFF'
  newMessage.textAvatarTextColor = '#ffffff'
  
  isEditing.value = false
  editingIndex.value = -1
  messageDialogVisible.value = true
}

const closeMessageDialog = () => {
  messageDialogVisible.value = false
  isEditing.value = false
  editingIndex.value = -1
}

const handleCustomAvatarChange = (file) => {
  const reader = new FileReader()
  reader.onload = (e) => {
    newMessage.avatarUrl = e.target.result
  }
  reader.readAsDataURL(file.raw)
}

const deleteCustomAvatar = () => {
  newMessage.avatarUrl = null
}

const updateAvatarPreview = () => {
  if (newMessage.avatarType === 'custom' && !newMessage.avatarUrl) {
    newMessage.avatarUrl = null
  }
}

const getAvatarPreviewText = () => {
  switch (newMessage.avatarType) {
    case 'default':
      return newMessage.customText ? newMessage.customText.slice(0, 2) : splitUsername(newMessage.username, newMessage.customText)
    case 'none':
      return '默认'
    case 'custom':
      return '自定义'
    default:
      return '预览'
  }
}

const getAvatarTypeLabel = () => {
  switch (newMessage.avatarType) {
    case 'default':
      return newMessage.customText ? `文字头像: ${newMessage.customText}` : '文字头像'
    case 'none':
      return hasDefaultAvatar.value ? '使用默认头像' : '暂无默认头像'
    case 'custom':
      return newMessage.avatarUrl ? '自定义头像' : '点击上传头像'
    default:
      return '头像预览'
  }
}

const applyColorPreset = (preset) => {
  newMessage.textAvatarBgColor = preset.bg
  newMessage.textAvatarTextColor = preset.text
  updateAvatarPreview()
}

const saveMessage = () => {
  if (!newMessage.username.trim() || !newMessage.message.trim()) {
    ElMessage.error('用户名和消息内容不能为空')
    return
  }
  
  let avatarUrl = 'default'
  if (newMessage.avatarType === 'none') {
    avatarUrl = null
  } else if (newMessage.avatarType === 'custom') {
    avatarUrl = newMessage.avatarUrl
  }
  
  const messageData = {
    username: newMessage.username,
    message: newMessage.message,
    isSelf: newMessage.isSelf,
    timestamp: Date.now(),
    avatarUrl: avatarUrl,
    customText: newMessage.customText,
    textAvatarBgColor: newMessage.textAvatarBgColor,
    textAvatarTextColor: newMessage.textAvatarTextColor
  }
  
  if (isEditing.value) {
    messages.value[editingIndex.value] = messageData
  } else {
    messages.value.push(messageData)
    currentMessageIndex.value = messages.value.length - 1
  }
  
  updatePreview()
  closeMessageDialog()
}

const splitUsername = (username, customText = '') => {
  // 如果有自定义文字，优先使用自定义文字
  if (customText && customText.trim()) {
    return customText.trim().slice(0, 2)
  }
  
  // 否则使用用户名逻辑
  if (username.indexOf('（') !== -1) {
    return username.slice(0, username.indexOf('（')).slice(-2)
  }
  if (username.indexOf('(') !== -1) {
    return username.slice(0, username.indexOf('(')).slice(-2)
  }
  return username.slice(-2)
}

const getAvatarUrl = (message) => {
  if (message.avatarUrl === null) {
    // 使用默认头像
    return message.isSelf ? settings.selfAvatarUrl : settings.otherAvatarUrl
  } else if (message.avatarUrl === 'default' || !message.avatarUrl) {
    return null // 使用文字头像
  } else {
    return message.avatarUrl // 使用自定义头像
  }
}

const updatePreview = () => {
  // 预览会自动更新，因为使用了响应式数据
}

const downloadScreenshot = async () => {
  if (messages.value.length === 0) {
    ElMessage.warning('请先添加消息生成聊天内容')
    return
  }
  
  try {
    await nextTick()
    const canvas = await html2canvas(chatPreview.value, {
      backgroundColor: settings.bgColor,
      scale: 2,
      logging: false,
      useCORS: true,
      allowTaint: true
    })
    
    const link = document.createElement('a')
    link.download = '聊天截图_' + new Date().getTime() + '.png'
    link.href = canvas.toDataURL()
    link.click()
    
    ElMessage.success('截图下载成功')
  } catch (err) {
    console.error('截图生成失败:', err)
    ElMessage.error('截图生成失败，请重试')
  }
}

const setAweiAvatar = () => {
  settings.otherAvatarUrl = 'https://img10.360buyimg.com/ddimg/jfs/t1/305052/18/13594/23198/685ca443F3774d9ab/967d365cbe09ae76.jpg'
  updatePreview()
  ElMessage.success('已设置阿伟头像')
}

// 生命周期
onMounted(() => {
  // 默认添加一条他人消息
  if (messages.value.length === 0) {
    messages.value.push({
      username: '赵津伟（迦勒）',
      message: '我也认同这个观点',
      isSelf: false,
      timestamp: Date.now(),
      avatarUrl: settings.otherAvatarUrl,
      customText: '',
      textAvatarBgColor: '#007AFF',
      textAvatarTextColor: '#ffffff'
    })
    currentMessageIndex.value = 0
  }
})
</script>

<style lang="scss" scoped>
@use './assets/main.scss';
</style>