<template>
  <div class="todos">
    <draggable v-model="todoCompleteList" group="todo" @end="onEnd">
      <div class="todo" :class="{ todoCheck: !todo.completed }" v-for="todo in getCompletedTodoUser"
           :key="todo.id">
        <div class="todo-header" :class="{ todoCheck: !todo.completed }">

          <p v-if="!todo.editModeTitle" @click="editTitleMode(todo)">{{ todo.title }}</p>
          <input v-model="newValueTitleTodo" v-if="todo.editModeTitle" :ref="'input_item_' + todo.id"
                 @blur="changeTitleButton(todo)" type="text">

          <p class="material-icons" @click="showPopupMoreInfoTodo(todo)">open_in_full</p>

        </div>
        <div class="todo-body">

          <p v-if="!todo.editModeDescription" @click="editDescriptionMode(todo)">{{ todo.description }}</p>
          <input v-model="newValueDescriptionTodo" v-if="todo.editModeDescription" :ref="'input_item_' + todo.id"
                 @blur="changeDescriptionButton(todo)" type="text">

        </div>
      </div>
    </draggable>

    <PopupMoreInfoAboutTodo
        v-if="isMoreInfoTodoPopupVisible"
        @closePopupMoreInfoTodo="closePopupMoreInfoTodo"
        :currentTodo="currentTodo"
    >
      <MoreInfoAboutTodo
          :todoInfo="currentTodo"
          @closePopupMoreInfoTodo="closePopupMoreInfoTodo"
      />
    </PopupMoreInfoAboutTodo>

  </div>
</template>

<script>
import {mapActions, mapGetters} from 'vuex'
import draggable from 'vuedraggable'
import PopupMoreInfoAboutTodo from '@/components/popup/PopupMoreInfoAboutTodo'
import MoreInfoAboutTodo from '@/components/MoreInfoAboutTodo'

export default {
  name: "CompletedTodo",
  components: {MoreInfoAboutTodo, PopupMoreInfoAboutTodo, draggable},
  data() {
    return {
      newValueTitleTodo: '',
      newValueDescriptionTodo: '',
      isMoreInfoTodoPopupVisible: false,
      currentTodo: {},
    }
  },
  methods: {
    ...mapActions(['removeTodoCompleted', 'changeTitleActiveTodo', 'changeDescriptionActiveTodo', 'updateLocalList']),
    onEnd() {
      this.updateLocalList()
    },
    closePopupMoreInfoTodo() {
      this.isMoreInfoTodoPopupVisible = false
    },
    showPopupMoreInfoTodo(todo) {
      this.currentTodo = todo
      this.isMoreInfoTodoPopupVisible = true
    },
    editTitleMode: function (todo) {
      const findTodo = this.getCompletedTodoUser.find(t => t.id === todo.id)

      findTodo.editModeTitle = true

      this.$nextTick(() => {
        this.$refs['input_item_' + todo.id][0].focus();
      });

    },
    changeTitleButton(todo) {

      const findTodo = this.getCompletedTodoUser.find(t => t.id === todo.id)

      findTodo.editModeTitle = false

      if (this.newValueTitleTodo.trim()) {
        let updatedTitle = {
          id: todo.id,
          title: this.newValueTitleTodo
        }

        this.changeTitleActiveTodo(updatedTitle)
      }
    },
    editDescriptionMode: function (todo) {
      const findTodo = this.getCompletedTodoUser.find(t => t.id === todo.id)

      findTodo.editModeDescription = true

      this.$nextTick(() => {
        this.$refs['input_item_' + todo.id][0].focus();
      });
    },
    changeDescriptionButton(todo) {

      const findTodo = this.getCompletedTodoUser.find(t => t.id === todo.id)

      findTodo.editModeDescription = false

      if (this.newValueDescriptionTodo.trim()) {
        let updatedDescription = {
          id: todo.id,
          description: this.newValueDescriptionTodo
        }

        this.changeDescriptionActiveTodo(updatedDescription)
      }
    },
  },
  computed: {
    ...mapGetters(['getCompletedTodoUser']),
    todoCompleteList: {
      get() {
        return this.getCompletedTodoUser
      },
      set(value) {
        this.$store.dispatch("updateCompleteTodo", value);
      }
    }
  }
}
</script>

<style scoped>
.todo {
  background-color: #26de81;
}
</style>