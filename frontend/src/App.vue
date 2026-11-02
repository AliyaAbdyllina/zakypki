<script setup>
import { ref, watch } from 'vue'

// Какой экран показан: 'list' | 'new' | 'login'
const screen = ref('list')

// Сообщение о том, что форма пока является макетом
const notice = ref('')

// Поле заголовка: получает фокус при открытии формы создания
const titleInput = ref(null)

const screens = [
  { id: 'list', label: 'Список заявок' },
  { id: 'new', label: 'Создать заявку' },
  { id: 'login', label: 'Вход' },
]

// Подписи статусов из ЛР1. В данных хранится только код.
const statusLabels = {
  New: 'Новая',
  InProgress: 'В работе',
  Closed: 'Закупка завершена',
  Cancelled: 'Отменена',
}

// Демонстрационные записи ЛР3: настоящих данных пока нет.
const items = [
  {
    id: 1,
    number: 'ЗК-101',
    title: 'Закупка алюминиевого листа',
    materialId: 2,
    description: 'Лист для партии кастрюль на следующий месяц',
    assigneeUserId: null,
    status: 'New',
  },
  {
    id: 2,
    number: 'ЗК-102',
    title: 'Закупка стального листа',
    materialId: 1,
    description: 'Лист для производства крышек',
    assigneeUserId: 5,
    status: 'InProgress',
  },
]

function showNotice(text) {
  notice.value = text
}

watch(
  screen,
  (current) => {
    notice.value = ''
    if (current === 'new') {
      titleInput.value?.focus()
    }
  },
  { flush: 'post' },
)
</script>

<template>
  <div class="layout">
    <header class="header">
      <div>
        <p class="system">Система управления заявками на закупку материалов</p>
        <p class="organization">АО «Кукморский завод Металлопосуды», отдел снабжения</p>
      </div>

      <nav aria-label="Экраны приложения" class="nav">
        <button
          v-for="item in screens"
          :key="item.id"
          type="button"
          class="nav-button"
          :class="{ active: screen === item.id }"
          :aria-current="screen === item.id ? 'page' : undefined"
          @click="screen = item.id"
        >
          {{ item.label }}
        </button>
      </nav>
    </header>

    <main class="main">
      <section v-if="screen === 'list'">
        <h1>Список заявок</h1>
        <p class="hint">Демонстрационные записи. Названия материалов и исполнителей появятся позже, пока показаны их номера.</p>

        <div class="table-wrap">
          <table>
            <thead>
              <tr>
                <th scope="col">Номер</th>
                <th scope="col">Заголовок</th>
                <th scope="col">Материал</th>
                <th scope="col">Исполнитель</th>
                <th scope="col">Статус</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in items" :key="item.id">
                <td class="nowrap">{{ item.number }}</td>
                <td>{{ item.title }}</td>
                <td>№ {{ item.materialId }}</td>
                <td>{{ item.assigneeUserId === null ? 'Не назначен' : `№ ${item.assigneeUserId}` }}</td>
                <td class="nowrap">
                  <span class="status" :class="`status-${item.status}`">
                    {{ statusLabels[item.status] }} ({{ item.status }})
                  </span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </section>

      <section v-else-if="screen === 'new'" class="form-section">
        <h1>Создать заявку</h1>
        <p class="hint">Макет формы, проверка и сохранение появятся в следующих работах.</p>

        <form @submit.prevent="showNotice('Это макет, заявка не сохранена. Сохранение в ЛР5.')">
          <div class="field">
            <label for="title">Заголовок, 5–80 символов</label>
            <input id="title" ref="titleInput" name="title" type="text" autofocus>
          </div>

          <div class="field">
            <label for="materialId">Номер материала</label>
            <input id="materialId" name="materialId" type="number">
          </div>

          <div class="field">
            <label for="description">Описание, 10–500 символов</label>
            <textarea id="description" name="description" rows="5"></textarea>
          </div>

          <button type="submit" class="primary">Создать</button>
          <p v-if="notice" class="notice" role="status">{{ notice }}</p>
        </form>
      </section>

      <section v-else class="form-section">
        <h1>Вход</h1>
        <p class="hint">Макет формы, настоящий вход появится в ЛР11.</p>

        <form @submit.prevent="showNotice('Это макет, логин и пароль не проверяются')">
          <div class="field">
            <label for="login">Логин</label>
            <input id="login" name="login" type="text" autocomplete="username">
          </div>

          <div class="field">
            <label for="password">Пароль</label>
            <input id="password" name="password" type="password" autocomplete="current-password">
          </div>

          <button type="submit" class="primary">Войти</button>
          <p v-if="notice" class="notice" role="status">{{ notice }}</p>
        </form>
      </section>
    </main>
  </div>
</template>

<style scoped>
.layout {
  min-height: 100vh;
}

.header {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
  gap: 12px 32px;
  padding: 14px 32px;
  color: #fff;
  background: #1f3a5f;
}

.system {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
}

.organization {
  margin: 2px 0 0;
  font-size: 13px;
  color: #c9d6e6;
}

.nav {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
}

.nav-button {
  padding: 8px 14px;
  font: inherit;
  font-size: 14px;
  color: #fff;
  cursor: pointer;
  background: transparent;
  border: 1px solid #5d7a9f;
  border-radius: 4px;
}

.nav-button:hover {
  background: #2c4d7a;
}

.nav-button.active {
  font-weight: 600;
  color: #1f3a5f;
  background: #fff;
  border-color: #fff;
}

.main {
  max-width: 1040px;
  padding: 28px 32px 48px;
  margin: 0 auto;
}

h1 {
  margin: 0 0 6px;
  font-size: 24px;
  font-weight: 600;
}

.hint {
  margin: 0 0 20px;
  font-size: 14px;
  color: #55606e;
}

.table-wrap {
  overflow-x: auto;
  background: #fff;
  border: 1px solid #d5dbe3;
  border-radius: 4px;
}

table {
  width: 100%;
  font-size: 14px;
  border-collapse: collapse;
}

th,
td {
  padding: 11px 14px;
  text-align: left;
  border-bottom: 1px solid #e4e8ee;
}

th {
  font-weight: 600;
  color: #3a4552;
  background: #f1f4f8;
}

tbody tr:last-child td {
  border-bottom: 0;
}

.nowrap {
  white-space: nowrap;
}

.status {
  display: inline-block;
  padding: 3px 8px;
  font-size: 13px;
  border: 1px solid;
  border-radius: 4px;
}

.status-New {
  color: #1f4f8b;
  background: #e9f1fb;
}

.status-InProgress {
  color: #7a4d00;
  background: #fdf3df;
}

.status-Closed {
  color: #1d6336;
  background: #e6f4ea;
}

.status-Cancelled {
  color: #5a6470;
  background: #eef0f3;
}

.form-section {
  max-width: 560px;
}

form {
  padding: 24px;
  background: #fff;
  border: 1px solid #d5dbe3;
  border-radius: 4px;
}

.field {
  margin-bottom: 18px;
}

label {
  display: block;
  margin-bottom: 6px;
  font-size: 14px;
  font-weight: 600;
}

input,
textarea {
  box-sizing: border-box;
  width: 100%;
  padding: 9px 11px;
  font: inherit;
  font-size: 15px;
  color: inherit;
  background: #fff;
  border: 1px solid #aeb7c2;
  border-radius: 4px;
}

textarea {
  resize: vertical;
}

input:focus,
textarea:focus,
button:focus-visible {
  outline: 2px solid #2f6db5;
  outline-offset: 1px;
}

.primary {
  padding: 10px 20px;
  font: inherit;
  font-size: 15px;
  font-weight: 600;
  color: #fff;
  cursor: pointer;
  background: #1f3a5f;
  border: 1px solid #1f3a5f;
  border-radius: 4px;
}

.primary:hover {
  background: #2c4d7a;
}

.notice {
  padding: 10px 12px;
  margin: 16px 0 0;
  font-size: 14px;
  color: #1f4f8b;
  background: #e9f1fb;
  border: 1px solid #b9d2ef;
  border-radius: 4px;
}

@media (max-width: 600px) {
  .header,
  .main {
    padding-right: 16px;
    padding-left: 16px;
  }
}
</style>
