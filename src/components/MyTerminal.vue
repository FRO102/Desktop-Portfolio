<template>
  <!-- Terminal Window Container -->
  <div class="terminal-container">
    <!-- Terminal Content -->
    <div
      ref="terminalRef"
      class="terminal-content"
      @click="focusInput"
    >
      <!--      <div class="border-l-2 border-green-400 pl-4">
        <div class="text-green-400 font-semibold text-lg">🖥️ Interactive Desktop Portfolio</div>
        <div class="text-yellow-400 text-sm mb-2">Vue.js 3 • TailwindCSS • Vite | 2024</div>
        <div class="text-gray-300 mb-2">
          Modern portfolio website designed as an interactive desktop environment with<br>
          terminal interface, window management, and responsive design. Built using<br>
          technologies from my current skill set including Vue.js and TailwindCSS.
        </div>
        <div class="text-blue-400 text-sm">
          <span class="font-semibold">Tech Stack:</span> Vue 3, TailwindCSS, JavaScript, Git
        </div>
        <div class="text-cyan-400 text-sm">
          🔗 <span class="underline">github.com/FRO102/Desktop-Portfolio</span>
        </div>
      </div>ge -->
      <div class="mb-4">
        <div class="text-green-400">Welcome to my Terminal Portfolio</div>
        <div class="text-green-400">Type 'help' to see available commands.</div>
      </div>

      <!-- Terminal Output -->
      <div v-for="(line, idx) in output" :key="`line-${idx}`" class="mb-1">
        <div v-if="line.type === 'text'" v-html="line.content"></div>
        <div v-else-if="line.type === 'help'" class="mt-2">
          <div class="text-white font-bold mb-2">Available Commands:</div>
          <div class="grid grid-cols-1 gap-1">
            <div v-for="cmd in commands" :key="cmd.command" class="flex">
              <button
                @click="runCommand(cmd.command)"
                class="command-button"
                type="button"
              >
                {{ cmd.label }}
              </button>
              <span class="text-gray-400 ml-4">- {{ cmd.desc }}</span>
            </div>
          </div>
        </div>
      </div>

      <!-- Current Input Line -->
      <div class="prompt-line">
        <span class="prompt-text">
          <span class="text-green-400">visitor@portfolio</span>
          <span class="text-white">:</span>
          <span class="text-blue-400">~</span>
          <span class="text-white">$</span>
        </span>
        <input
          ref="inputRef"
          v-model="input"
          class="terminal-input"
          autocomplete="off"
          spellcheck="false"
          @keydown="handleKeydown"
          @blur="focusInput"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, nextTick, onUnmounted, computed } from "vue"

// Reactive state
const input = ref("")
const output = ref([])
const inputRef = ref(null)
const terminalRef = ref(null)
const commandHistory = ref([])
const showCursor = ref(true)

// Non-reactive state
let historyIndex = -1
let cursorInterval = null

// Constants
const PROMPT_PREFIX = `<span class="text-green-400">visitor@portfolio</span><span class="text-white">:</span><span class="text-blue-400">~</span><span class="text-white">$`

const commands = [
  { label: "help", command: "help", desc: "Show available commands" },
  { label: "whoami", command: "whoami", desc: "Show about information" },
  { label: "skills", command: "skills", desc: "Show technical skills" },
  { label: "projects", command: "projects", desc: "Show portfolio projects" },
  { label: "experience", command: "experience", desc: "Show professional experience" },
  { label: "education", command: "education", desc: "Show academic background" },
  { label: "contact", command: "contact", desc: "Show contact information" },
  { label: "cv", command: "cv", desc: "Download CV" },
  { label: "clear", command: "clear", desc: "Clear the screen" },
]

// Command content templates
const commandContent = {
  whoami: `<div class="text-gray-100">
    <div class="text-cyan-400 font-bold text-lg mb-3">Frederico Oliveira</div>   
    <div class="mt-3 text-gray-200 leading-relaxed">
      <div class="text-green-400 font-semibold mb-2">ABOUT ME:</div>
      <div class="text-gray-300">
        Computer Engineering student, currently in my third year. Proactive, analytical, and focused on<br>
        efficient solutions. Enthusiastic about computer systems and fascinated by systems, services,<br>
        and infrastructure management, I always strive to stay up to date in order to develop my<br>
        knowledge and advance in the field.
      </div>
    </div>
    
    <div class="mt-4 text-gray-300">
      <div class="text-blue-400 font-semibold mb-1">Languages:</div>
      <div class="ml-2">
        • <span class="text-green-400">Portuguese</span> (Native)<br>
        • <span class="text-green-400">English</span> (B1 Level - Independent User)
      </div>
    </div>
    
    <div class="mt-3 text-gray-300">
      <div class="text-purple-400 font-semibold">Driving License:</div>
      <div class="ml-2">• Category B</div>
    </div>
  </div>`,
  
  skills: `<div class="text-gray-100">
    <div class="text-yellow-400 font-bold text-lg mb-3">Technical Skills & Digital Competencies</div>
    
    <div class="mb-4">
      <div class="text-green-400 font-semibold mb-2">Key Digital Skills:</div>
      <div class="grid grid-cols-2 md:grid-cols-3 gap-2 ml-2 text-gray-300">
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-yellow-400">Networking</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-yellow-400">AWS Cloud</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-yellow-400">Proxmox</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-yellow-400">ESXi</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-yellow-400">Citrix</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-yellow-400">VMware</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-yellow-400">VirtualBox</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-yellow-400">Linux</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-yellow-400">Git</span>
        </div>
      </div>
    </div>
    
    <div class="mb-4">
      <div class="text-green-400 font-semibold mb-2">⚡ Programming Languages:</div>
      <div class="grid grid-cols-2 md:grid-cols-3 gap-2 ml-2 text-gray-300">
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-blue-400">C</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-blue-400">Java</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-blue-400">C#</span>
        </div>
      </div>
    </div>
    
    <div class="mb-4">
      <div class="text-green-400 font-semibold mb-2">🌐 Web Technologies:</div>
      <div class="grid grid-cols-2 md:grid-cols-3 gap-2 ml-2 text-gray-300">
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-green-400">Vue.js</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-green-400">Laravel</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-green-400">TailwindCSS</span>
        </div>
        <div class="flex items-center">
          <span class="text-cyan-400 mr-2">•</span>
          <span class="text-green-400">API Development</span>
        </div>
      </div>
    </div>
    
    <div class="mt-4 p-3 bg-gray-800 rounded border-l-4 border-blue-400">
      <div class="text-blue-400 font-semibold mb-1">🏅 Specializations:</div>
      <div class="text-gray-300 text-sm">
        • Infrastructure Management & Virtualization<br>
        • Cloud Computing (AWS)<br>
        • System Administration<br>
        • Network Configuration
      </div>
    </div>
  </div>`,
  
  projects: `<div class="text-gray-100">
    <div class="text-cyan-400 font-bold text-lg mb-3">Featured Projects</div>
    <div class="space-y-4">
      <div class="border-l-2 border-green-400 pl-4">
        <div class="text-green-400 font-semibold text-lg">�️ Interactive Desktop Portfolio</div>
        <div class="text-yellow-400 text-sm mb-2">Vue.js 3 • TailwindCSS • Vite | 2024</div>
        <div class="text-gray-300 mb-2">
          Modern portfolio website designed as an interactive desktop environment.<br>
          Features window management, terminal interface, and responsive design.
        </div>
        <div class="text-blue-400 text-sm">
          <span class="font-semibold">Tech Stack:</span> Vue 3, Composition API, TailwindCSS, JavaScript
        </div>
        <div class="text-cyan-400 text-sm">
          🔗 <span class="underline">github.com/FRO102/Desktop-Portfolio</span>
        </div>
      </div>
      
      <div class="border-l-2 border-blue-400 pl-4">
        <div class="text-blue-400 font-semibold text-lg">� E-commerce Platform</div>
        <div class="text-yellow-400 text-sm mb-2">MERN Stack • Redux • Stripe API | 2024</div>
        <div class="text-gray-300 mb-2">
          Full-stack e-commerce solution with user authentication, product management,<br>
          shopping cart functionality, and secure payment processing.
        </div>
        <div class="text-blue-400 text-sm">
          <span class="font-semibold">Tech Stack:</span> React, Node.js, MongoDB, Express, Stripe
        </div>
        <div class="text-cyan-400 text-sm">
          🔗 <span class="underline">github.com/FRO102/ecommerce-platform</span>
        </div>
      </div>
      
      <div class="border-l-2 border-purple-400 pl-4">
        <div class="text-purple-400 font-semibold text-lg">� Task Management System</div>
        <div class="text-yellow-400 text-sm mb-2">Vue.js • Python FastAPI • PostgreSQL | 2023</div>
        <div class="text-gray-300 mb-2">
          Collaborative project management tool with real-time updates, team collaboration,<br>
          task assignment, and progress tracking features.
        </div>
        <div class="text-blue-400 text-sm">
          <span class="font-semibold">Tech Stack:</span> Vue.js, FastAPI, PostgreSQL, WebSockets
        </div>
        <div class="text-cyan-400 text-sm">
          🔗 <span class="underline">github.com/FRO102/task-manager</span>
        </div>
      </div>
      
      <div class="border-l-2 border-orange-400 pl-4">
        <div class="text-orange-400 font-semibold text-lg">🌐 Personal Blog Platform</div>
        <div class="text-yellow-400 text-sm mb-2">Next.js • TypeScript • Prisma | 2023</div>
        <div class="text-gray-300 mb-2">
          Modern blog platform with CMS functionality, SEO optimization,<br>
          and markdown support for content creation.
        </div>
        <div class="text-blue-400 text-sm">
          <span class="font-semibold">Tech Stack:</span> Next.js, TypeScript, Prisma, PostgreSQL
        </div>
        <div class="text-cyan-400 text-sm">
          🔗 <span class="underline">github.com/FRO102/blog-platform</span>
        </div>
      </div>
    </div>
  </div>`,
  
  experience: `<div class="text-gray-100">
    <div class="text-yellow-400 font-bold text-lg mb-3">Work Experience</div>
    
    <div class="border-l-2 border-green-400 pl-4">
      <div class="text-green-400 font-semibold text-lg">Intern - Help Desk</div>
      <div class="text-cyan-400 mb-3">Capgemini • Lisboa, Portugal</div>
      
      <div class="space-y-3">
        <div>
          <div class="text-blue-400 font-semibold mb-1">👥 User service and technical support</div>
          <div class="ml-2 text-gray-300 space-y-1">
            <div>• Respond to requests for help by phone or email</div>
            <div>• Diagnose and resolve basic technical problems (hardware and software)</div>
          </div>
        </div>
        
        <div>
          <div class="text-blue-400 font-semibold mb-1">⚙️ Equipment installation and configuration</div>
          <div class="ml-2 text-gray-300 space-y-1">
            <div>• Install and configure computers, printers, and software</div>
            <div>• Perform basic updates and maintenance</div>
          </div>
        </div>
        
        <div>
          <div class="text-blue-400 font-semibold mb-1">📚 User training and guidance</div>
          <div class="ml-2 text-gray-300 space-y-1">
            <div>• Explain how to use tools and systems in simple terms</div>
            <div>• Promote best practices for technology use</div>
          </div>
        </div>
        
        <div>
          <div class="text-blue-400 font-semibold mb-1">🔧 Monitoring and preventive maintenance</div>
          <div class="ml-2 text-gray-300 space-y-1">
            <div>• Check the operating status of systems and equipment</div>
            <div>• Help prevent failures and data loss</div>
          </div>
        </div>
      </div>
      
      <div class="mt-3 p-2 bg-gray-800 rounded">
        <div class="text-orange-400 text-sm">
          <span class="font-semibold">Company:</span> Capgemini - Global technology consulting and services company
        </div>
      </div>
    </div>
  </div>`,
  
  education: `<div class="text-gray-100">
    <div class="text-cyan-400 font-bold text-lg mb-3">Education and Training</div>
    <div class="space-y-4">
      <div class="border-l-2 border-blue-400 pl-4">
        <div class="text-blue-400 font-semibold text-lg">Bachelor's Degree in Computer Engineering</div>
        <div class="text-yellow-400 mb-2">Instituto Politécnico de Leiria - Escola Superior de Tecnologia e Gestão</div>
        <div class="text-green-400 text-sm mb-2">[ Current ] • Leiria, Portugal • EQF Level 6</div>
        
        <div class="text-gray-300 mb-3">
          Currently pursuing a comprehensive Computer Engineering degree focused on<br>
          modern software development, system administration, and emerging technologies.
        </div>
        
        <div class="text-cyan-400 text-sm mb-2">
          🌐 <span class="underline">www.ipleiria.pt/estg/</span>
        </div>
        
        <div class="text-orange-400 text-sm">
          <span class="font-semibold">Field of Study:</span> Information Technology & Computer Engineering
        </div>
      </div>
      
      <div class="border-l-2 border-green-400 pl-4">
        <div class="text-green-400 font-semibold text-lg">High School - Professional Course</div>
        <div class="text-yellow-400 mb-2">Computer Equipment Management and Technology</div>
        <div class="text-purple-400 mb-2">Instituto dos Pupilos do Exército • Lisboa, Portugal</div>
        <div class="text-green-400 text-sm mb-2">EQF Level 4</div>
        
        <div class="text-gray-300 mb-3">
          Professional technical course focused on computer systems management,<br>
          hardware maintenance, and technology infrastructure.
        </div>
        
        <div class="text-cyan-400 text-sm mb-2">
          🌐 <span class="underline">pupilos.pt</span>
        </div>
        
        <div class="text-orange-400 text-sm">
          <span class="font-semibold">Specialization:</span> IT Equipment Management & Technology
        </div>
      </div>
      
      <div class="border-l-2 border-purple-400 pl-4">
        <div class="text-purple-400 font-semibold text-lg">Professional Certifications</div>
        <div class="space-y-3 mt-2">
          <div class="bg-gray-800 p-3 rounded">
            <div class="text-yellow-400 font-semibold">🏅 AWS Academy Graduate - AWS Academy Cloud Foundations</div>
            <div class="text-cyan-400 text-sm mt-1">
              🔗 <span class="underline">credly.com/badges/c8ef9340-6f73-4da4-baf4-c23590edd89f</span>
            </div>
          </div>
          
          <div class="bg-gray-800 p-3 rounded">
            <div class="text-yellow-400 font-semibold">🏅 AWS Academy Graduate - AWS Academy Cloud Security Foundations</div>
            <div class="text-cyan-400 text-sm mt-1">
              🔗 <span class="underline">credly.com/badges/4df0c06a-8ac9-44a3-a3c6-23afb18cc67e</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>`,
  
  contact: `<div class="text-gray-100">
    <div class="text-green-400 font-bold text-lg mb-3">📞 Contact Information</div>
    
    <div class="space-y-3">
      <div class="flex items-center space-x-3">
        <span class="text-blue-400 font-semibold">📧 Email:</span>
        <a href="mailto:froliveira10213@gmail.com" class="text-cyan-400 underline hover:text-cyan-300">
          froliveira10213@gmail.com
        </a>
      </div>
      
      <div class="flex items-center space-x-3">
        <span class="text-blue-400 font-semibold">💼 LinkedIn:</span>
        <a href="https://linkedin.com/in/frederico-oliveira" target="_blank" class="text-cyan-400 underline hover:text-cyan-300">
          linkedin.com/in/frederico-oliveira
        </a>
      </div>
      
      <div class="flex items-center space-x-3">
        <span class="text-blue-400 font-semibold">🐙 GitHub:</span>
        <a href="https://github.com/FRO102" target="_blank" class="text-cyan-400 underline hover:text-cyan-300">
          github.com/FRO102
        </a>
      </div>
      
      <div class="flex items-center space-x-3">
        <span class="text-blue-400 font-semibold">� Location:</span>
        <span class="text-cyan-400">Leiria, Portugal</span>
      </div>
      
      <div class="flex items-center space-x-3">
        <span class="text-blue-400 font-semibold">🇵🇹 Nationality:</span>
        <span class="text-cyan-400">Portuguese</span>
      </div>
    </div>
    
    <div class="mt-4 p-3 bg-gray-800 rounded border-l-4 border-green-400">
      <div class="text-yellow-400 font-semibold mb-1">🚀 Currently Available for:</div>
      <div class="text-gray-300 text-sm">
        • Internship opportunities in IT/Software Development<br>
        • Part-time or project-based work<br>
        • Collaboration on technology projects<br>
        • Technical support and help desk roles
      </div>
    </div>
    
    <div class="mt-3 p-3 bg-blue-900 rounded border-l-4 border-blue-400">
      <div class="text-blue-400 font-semibold mb-1">🎯 Career Interests:</div>
      <div class="text-gray-300 text-sm">
        • System Administration & Infrastructure<br>
        • Cloud Technologies (AWS)<br>
        • Web Development<br>
        • DevOps & Automation
      </div>
    </div>
    
    <div class="mt-3 text-gray-400 text-sm">
      <span class="text-green-400">💡 Tip:</span> Feel free to reach out via email for any opportunities or collaborations!
    </div>
  </div>`,
  
  cv: `<div class="text-gray-100">
    <div class="text-yellow-400">CV Files Available:</div>
    <div class="text-green-400 mt-1">
      ✓ <a href="/src/assets/Frederico_Oliveira_EN_Online.pdf" download class="text-cyan-400 underline hover:text-cyan-300">Frederico_Oliveira_EN.pdf</a> (English)
    </div>
    <div class="text-green-400 mt-1">
      ✓ <a href="/src/assets/Frederico_Oliveira_PT_Online.pdf" download class="text-cyan-400 underline hover:text-cyan-300">Frederico_Oliveira_PT.pdf</a> (Português)
    </div>
  </div>`,
}

// Utility functions
const addToOutput = (type, content) => {
  output.value.push({ type, content })
}

const addPromptToOutput = (command = '') => {
  addToOutput('text', `${PROMPT_PREFIX} ${command}</span>`)
}

const scrollToBottom = () => {
  nextTick(() => {
    if (terminalRef.value) {
      terminalRef.value.scrollTop = terminalRef.value.scrollHeight
    }
  })
}

const focusInput = () => {
  nextTick(() => {
    if (inputRef.value) {
      inputRef.value.focus()
    }
  })
}

// Command execution
const runCommand = (command) => {
  if (!command) return
  
  addPromptToOutput(command)
  
  switch (command) {
    case "help":
      addToOutput('help')
      break
    case "clear":
      output.value.splice(0)
      break
    case "whoami":
    case "skills":
    case "projects":
    case "experience":
    case "education":
    case "contact":
    case "cv":
      addToOutput('text', commandContent[command])
      break
    default:
      addToOutput('text', `<div class="text-red-400">${command}: command not found</div>`)
  }
  
  scrollToBottom()
}

// Keyboard event handling
const handleKeydown = (e) => {
  const { key, ctrlKey } = e
  
  if (key === "Enter") {
    handleEnterKey()
  } else if (key === "ArrowUp") {
    e.preventDefault()
    navigateHistory(-1)
  } else if (key === "ArrowDown") {
    e.preventDefault()
    navigateHistory(1)
  } else if (key === "Tab") {
    e.preventDefault()
    handleTabCompletion()
  } else if (key === "l" && ctrlKey) {
    e.preventDefault()
    runCommand("clear")
  }
}

const handleEnterKey = () => {
  const command = input.value.trim()
  
  if (command) {
    runCommand(command)
    updateCommandHistory(command)
  } else {
    addPromptToOutput()
  }
  
  resetInput()
}

const updateCommandHistory = (command) => {
  if (commandHistory.value[commandHistory.value.length - 1] !== command) {
    commandHistory.value.push(command)
  }
}

const resetInput = () => {
  historyIndex = -1
  input.value = ""
}

const navigateHistory = (direction) => {
  const historyLength = commandHistory.value.length
  if (historyLength === 0) return
  
  const newIndex = historyIndex + direction
  
  if (direction === -1 && newIndex < historyLength) {
    historyIndex = newIndex
    input.value = commandHistory.value[historyLength - 1 - historyIndex]
  } else if (direction === 1) {
    if (newIndex > 0) {
      historyIndex = newIndex - 1
      input.value = commandHistory.value[historyLength - 1 - historyIndex]
    } else {
      historyIndex = -1
      input.value = ""
    }
  }
}

const handleTabCompletion = () => {
  const inputVal = input.value.toLowerCase()
  const matches = commands
    .map(c => c.command)
    .filter(cmd => cmd.startsWith(inputVal))
  
  if (matches.length === 1) {
    input.value = matches[0]
  } else if (matches.length > 1) {
    const completion = getCommonPrefix(matches)
    
    if (completion.length > inputVal.length) {
      input.value = completion
    } else {
      showCompletions(matches)
    }
  }
}

const showCompletions = (matches) => {
  addPromptToOutput(input.value)
  addToOutput('text', `<div class="text-gray-300">${matches.join('  ')}</div>`)
  scrollToBottom()
}

const getCommonPrefix = (strings) => {
  if (strings.length === 0) return ""
  
  let prefix = strings[0]
  for (let i = 1; i < strings.length; i++) {
    while (strings[i].indexOf(prefix) !== 0) {
      prefix = prefix.substring(0, prefix.length - 1)
      if (prefix === "") return ""
    }
  }
  return prefix
}

// Lifecycle hooks
onMounted(() => {
  focusInput()
  
  // Cursor blinking effect
  cursorInterval = setInterval(() => {
    showCursor.value = !showCursor.value
  }, 500)
})

onUnmounted(() => {
  if (cursorInterval) {
    clearInterval(cursorInterval)
  }
})
</script>

<style scoped>
.terminal-container {
  background-color: rgb(17 24 39);
  border-radius: 0.5rem;
  box-shadow: 0 25px 50px -12px rgb(0 0 0 / 0.25);
  border: 1px solid rgb(55 65 81);
  height: 100%;
  display: flex;
  flex-direction: column;
}

.terminal-content {
  background-color: black;
  color: rgb(243 244 246);
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', 'Courier New', monospace;
  font-size: 0.875rem;
  padding: 1rem;
  height: 100%;
  overflow-y: auto;
  flex: 1;
  line-height: 1.625;
}

.prompt-line {
  display: flex;
  align-items: center;
  margin-top: 0.5rem;
}

.prompt-text {
  color: rgb(34 197 94);
  user-select: none;
}

.terminal-input {
  background-color: transparent;
  outline: none;
  color: rgb(243 244 246);
  flex: 1;
  margin-left: 0.25rem;
  caret-color: rgb(34 197 94);
}

.command-button {
  color: rgb(34 197 94);
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', 'Courier New', monospace;
  width: 4rem;
  text-align: left;
  transition: color 0.2s ease-in-out;
}

.command-button:hover {
  color: rgb(74 222 128);
}

.cursor {
  color: rgb(243 244 246);
  animation: blink 1s infinite;
}

/* Custom scrollbar */
.terminal-content::-webkit-scrollbar {
  width: 8px;
}

.terminal-content::-webkit-scrollbar-track {
  background: #1a1a1a;
}

.terminal-content::-webkit-scrollbar-thumb {
  background: #4a5568;
  border-radius: 4px;
}

.terminal-content::-webkit-scrollbar-thumb:hover {
  background: #68728a;
}

/* Selection styles */
::selection {
  background-color: #374151;
  color: #ffffff;
}

/* Terminal font improvements */
.font-mono {
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', 'Courier New', monospace;
  font-variant-ligatures: none;
}

/* Cursor animation */
@keyframes blink {
  0%, 50% { opacity: 1; }
  51%, 100% { opacity: 0; }
}
</style>
