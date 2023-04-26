<script setup>
import { onMounted } from 'vue'
import axios from 'axios'

// alert, confirm 쓰지 않기
// id 랜덤값 crypto.randomUUID()
// 함수명은 동사가 앞에있는, => getTodo
// 변수는 명사의 조합
// ID값으로 date를 쓰는건 안좋다. 동시에 쓸 수 있는 확률이 있다.(누가 동시에 엔터칠확률)
// array 함수는 고민해서 쓸것

/**
 * TODO
 * 서버 연동, 수정기능 추가
 * 완료된 건 전부 삭제하기 버튼 구현(여러 투두 한꺼번에 삭제)
 * POST 시 체크표시가 풀리는것을 어떻게 해결할지 고민해서 만들어보기
 */

// const count = 0
const todoValue = ref('')
const todoLists = ref([])
const todoInput = ref(null)
const targetUpdateTodo = ref('')
const updateTodoValue = ref('')
const baseUrl = 'http://localhost:3000'

const inputFocus = () => {
  todoInput.value.focus()
}

const getTodo = async () => {
  try {
    const response = await axios.get(`${baseUrl}/todos`)
    todoLists.value = response.data
  }
  catch (error) {
    console.error(error)
    errorMessage('요청에 실패했습니다. 잠시후 다시 시도해주세요.')
  }
}
getTodo()

const addTodo = async () => {
  if (todoValue.value.length < 1) {
    errorMessage('할일을 입력해주세요.')
    return
  }

  const newTodo = {
    todo: todoValue.value,
    createdAt: new Date(),
    done: false,
  }

  try {
    await axios.post(`${baseUrl}/todos`, newTodo, {
      headers: {
        'Content-type': 'application/json',
      },
    })
    successMessage('할일이 추가되었어요.')
    todoValue.value = ''
    inputFocus()
    getTodo()
  }
  catch (error) {
    errorMessage('요청에 실패했습니다. 잠시후 다시 시도해주세요.')
  }
}

function errorMessage(message) {
  ElMessage({
    showClose: true,
    message,
    type: 'error',
  })
}

function successMessage(message) {
  ElMessage({
    message,
    type: 'success',
  })
}

// const deleteTodo = (index) => {
//   if (window.confirm('정말 삭제하시겠어요?'))
//     todoLists.splice(index, 1)
//   successMessage('삭제에 성공했어요.')
//   inputFocus()
// }

const deleteTodo = async (todo) => {
  if (window.confirm('정말 삭제하시겠어요?')) {
    try {
      await axios.delete(`${baseUrl}/todos/${todo.id}`)
      successMessage('삭제되었어요.')
      inputFocus()
      getTodo()
    }
    catch (error) {
      errorMessage('삭제에 실패했어요. 다시 시도해 주세요.')
    }
  }
}

const editTodo = async (todo) => {
  const newEditTodo = {
    todo: updateTodoValue.value,
    createdAt: new Date(),
    done: false,
  }

  if (targetUpdateTodo.value.length < 1) {
    targetUpdateTodo.value = todo.id
    return
  }

  if (targetUpdateTodo.value === todo.id) {
    try {
      await axios.patch(`${baseUrl}/todos/${todo.id}`, newEditTodo, {
        headers: {
          'Content-type': 'application/json',
        },
      })
      targetUpdateTodo.value = ''
      successMessage('수정되었어요.')
      inputFocus()
      getTodo()
    }
    catch (error) {
      console.error(error)
    }
  }
}

const handleEditButtonClick = (todo) => {
  targetUpdateTodo.value = todo.id
  updateTodoValue.value = todo.todo
}

const completeTodoItem = () => {
  return todoLists.value.filter(item => item.done).length
}

onMounted(() => {
  inputFocus()
})
</script>

<template>
  <div class="h-[800px] bg-[#E6E6E6] flex flex-col items-center justify-center text-[#7888FF]" style="font-family: 'omyu_pretty';">
    <section class="flex flex-col items-center bg-white h-[600px] w-130 shadow-2xl rounded overflow-auto">
      <h1 class="text-4xl mt-15 mb-3">
        ToDoList
      </h1>
      <div class="flex items-center justify-center gap-4">
        <el-input ref="todoInput" v-model="todoValue" placeholder="할일을 입력해보세요." @keyup.enter="addTodo" />
        <el-button style="background-color: #7887FF; border: none;" type="primary" @click="addTodo">
          추가
        </el-button>
      </div>
      <div class="p-4 flex justify-between w-70 mt-0">
        <span>All Tasks : {{ todoLists.length }}</span>
        <span>Completed : {{ completeTodoItem() }}</span>
      </div>
      <div class="border-dashed border-1 border-gray-300 w-[80%]" />
      <ul class="flex flex-col gap-4 p-10">
        <li v-for="(todo) in todoLists" :key="`todo-id-${todo.id}`" class="flex items-center justify-between w-80 text-lg">
          <div class="flex flex-col items-start" :style="{ 'text-decoration': todo.done ? 'line-through' : 'none' }">
            <el-input v-if="targetUpdateTodo === todo.id" v-model="updateTodoValue" placeholder="수정할 내용을 입력해주세요." @keyup.enter="editTodo(todo)" />
            <el-checkbox v-else v-model="todo.done" :label="todo.todo" style="color:#7887FF" />
            <span class="text-sm">{{ todo.createdAt }}</span>
          </div>
          <div v-if="targetUpdateTodo !== todo.id" class="flex gap-2 cursor-pointer">
            <div i-carbon-edit @click="handleEditButtonClick(todo)" />
            <div i-carbon-delete @click="deleteTodo(todo)" />
          </div>
          <div v-else class="flex gap-2 cursor-pointer">
            <div @click="editTodo(todo)">
              수정
            </div>
            <div @click="targetUpdateTodo = ''">
              취소
            </div>
          </div>
        </li>
      </ul>
    </section>
  </div>
</template>

<style lang="scss" scoped>
@font-face {
    font-family: 'omyu_pretty';
    src: url('https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_2304-01@1.0/omyu_pretty.woff2') format('woff2');
    font-weight: normal;
    font-style: normal;
}
</style>
