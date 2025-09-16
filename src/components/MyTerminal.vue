<template>
  <!-- Container principal do terminal -->
  <div
    class="bg-black text-green-400 font-mono p-4 h-full w-full overflow-auto"
    ref="terminalRef"
  >
    <!-- Mensagem de boas-vindas -->
    <div>Welcome to my terminal portfolio!</div>

    <!-- Output do terminal -->
    <div v-for="(line, idx) in output" :key="idx" class="mb-2">
      <div v-if="line.type === 'text'" v-html="line.content"></div>
      <div v-else-if="line.type === 'help'">
        <div class="mb-4">
          <div class="text-green-400">visitor@portfolio:~$ help</div>
          <div class="ml-4">
            <p class="text-cyan-400 mb-3 font-bold">📋 Available Commands:</p>
            <div class="space-y-2">
              <button
                v-for="cmd in commands"
                :key="cmd.label"
                @click="runCommand(cmd.command)"
                class="bg-gray-800 hover:bg-cyan-700 text-yellow-400 font-mono font-bold min-w-fit px-3 py-2 rounded transition-colors flex items-center space-x-4 mb-2"
                type="button"
              >
                <span>{{ cmd.label }}</span>
                <span class="text-gray-300 text-sm flex-1">{{ cmd.desc }}</span>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Input -->
    <div class="flex items-center">
      <span class="text-green-400 mr-2">visitor@portfolio:~$</span>
      <input
        v-model="input"
        @keydown="handleKeydown"
        ref="inputRef"
        class="bg-transparent outline-none text-green-400 flex-1"
        autofocus
      />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, nextTick } from "vue"

const input = ref("")
const output = ref([
  { type: "text", content: "Type 'help' to see available commands." },
])
const inputRef = ref(null)
const terminalRef = ref(null)
const commandHistory = ref([])
let historyIndex = -1
let commandCount = 0
let sessionStartTime = Date.now()

// Lista de comandos suportados
const commands = [
  { label: "help", command: "help", desc: "Show available commands" },
  { label: "about", command: "about", desc: "Show about information" },
  { label: "skills", command: "skills", desc: "Show technical skills" },
  { label: "projects", command: "projects", desc: "Show portfolio projects" },
  { label: "experience", command: "experience", desc: "Show professional experience" },
  { label: "education", command: "education", desc: "Show academic background" },
  { label: "contact", command: "contact", desc: "Show contact information" },
  { label: "cv", command: "cv", desc: "Download CV" },
  { label: "uptime", command: "uptime", desc: "Show session uptime" },
  { label: "clear", command: "clear", desc: "Clear the screen" },
]



// Executa comandos
function runCommand(command) {
  if (!command) return
  commandCount++
  output.value.push({ type: "text", content: `visitor@portfolio:~$ ${command}` })

  switch (command) {
    case "help":
      output.value.push({ type: "help" })
      break
    case "about":
      output.value.push({
        type: "text",
        content: "<p>👋 Hi, I'm <b>John Doe</b>, a passionate Web Developer!</p>",
      })
      break
    case "skills":
      output.value.push({
        type: "text",
        content: `
          <p>💻 <b>Skills:</b></p>
          <ul class="list-disc ml-6">
            <li>Vue.js, React, TailwindCSS</li>
            <li>Node.js, Express</li>
            <li>MongoDB, PostgreSQL</li>
            <li>Linux, Docker</li>
          </ul>
        `,
      })
      break
    case "projects":
      output.value.push({
        type: "text",
        content: `
          <p>📂 <b>Projects:</b></p>
          <ul class="list-disc ml-6">
            <li><a href="#" class="text-cyan-400 underline">Portfolio Website</a></li>
            <li><a href="#" class="text-cyan-400 underline">E-commerce App</a></li>
          </ul>
        `,
      })
      break
    case "experience":
      output.value.push({
        type: "text",
        content: `
          <p>💼 <b>Experience:</b></p>
          <p>Frontend Developer @ TechCorp (2021 - Present)</p>
        `,
      })
      break
    case "education":
      output.value.push({
        type: "text",
        content: `
          <p><b>Education:</b></p>
          <p>BSc Computer Science - University X</p>
        `,
      })
      break
    case "contact":
      output.value.push({
        type: "text",
        content: `
          <p><b>Contact:</b></p>
          <p>Email: <a href="mailto:john@example.com" class="text-cyan-400 underline">john@example.com</a></p>
        `,
      })
      break
    case "cv":
      output.value.push({
        type: "text",
        content: `<p>📄 <a href="/cv.pdf" download class="text-cyan-400 underline">Download CV</a></p>`,
      })
      break
    case "clear":
      output.value.splice(0)
      break
    default:
      output.value.push({
        type: "text",
        content: `<span class="text-red-400">❌ Command not found: ${command}</span>`,
      })
  }

  nextTick(() => {
    if (terminalRef.value) {
      terminalRef.value.scrollTop = terminalRef.value.scrollHeight
    }
  })
}

// Teclas
function handleKeydown(e) {
  if (e.key === "Enter") {
    runCommand(input.value.trim())
    if (
      input.value.trim() &&
      commandHistory.value[commandHistory.value.length - 1] !== input.value.trim()
    ) {
      commandHistory.value.push(input.value.trim())
    }
    historyIndex = -1
    input.value = ""
  } else if (e.key === "ArrowUp") {
    e.preventDefault()
    if (commandHistory.value.length > 0 && historyIndex < commandHistory.value.length - 1) {
      historyIndex++
      input.value = commandHistory.value[commandHistory.value.length - 1 - historyIndex]
    }
  } else if (e.key === "ArrowDown") {
    e.preventDefault()
    if (historyIndex > 0) {
      historyIndex--
      input.value = commandHistory.value[commandHistory.value.length - 1 - historyIndex]
    } else if (historyIndex === 0) {
      historyIndex = -1
      input.value = ""
    }
  } else if (e.key === "Tab") {
    e.preventDefault()
    const inputVal = input.value.toLowerCase()
    const matches = commands.map(c => c.command).filter(cmd => cmd.startsWith(inputVal))
    if (matches.length === 1) {
      input.value = matches[0]
    } else if (matches.length > 1) {
      const completion = getCommonPrefix(matches)
      if (completion.length > inputVal.length) {
        input.value = completion
      }
    }
  }
}

// Autocomplete prefixo
function getCommonPrefix(strings) {
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

onMounted(() => {
  inputRef.value.focus()
})
</script>
