<template>
    <div id="indexMemo">    
    <div class="indexMemo-main-container">
      
      <div class="index-memo">
        <h2>一覧</h2>
        <div v-if="!editMemoVisible">
          <ul 
            v-for="item in memos"
            v-bind:key="item.id"
          >        
            <li >
              <a v-show="!item.edit" href="#" v-on:click="doEdit(item)">
                {{ item.comment.split('\n')[0] }}
              </a>
            </li>
          </ul>
        </div>
        <div v-if="editMemoVisible">
          <ul  
            v-for="item in memos"
            v-bind:key="item.id"
          >
            <li >
              <a v-show="item.edit" class="red" href="#" v-on:click="notEdit(item)">
                {{ item.comment.split('\n')[0] }}
              </a>
            </li>
          </ul>
        </div>
      </div>
      <div class="create-memo" v-show="createMemoVisible">
        <h2>メモの追加</h2>
        <form class="add-form" v-on:submit.prevent="doAdd">
          <textarea id="memo-textarea" ref="comment" cols="30" rows="10"></textarea>
          <button type="submit" v-on:click="createMemoVisible = !createMemoVisible">追加</button>
          <button type="button" v-on:click="createMemoVisible = !createMemoVisible">キャンセル</button>
        </form>
      </div>
      <a v-show="!createMemoVisible" href="#" v-on:click="createMemoVisible = !createMemoVisible">+</a>
    </div>
    <div v-if="editMemoVisible" class="indexMemo-edit-container">
      
      <p>メモの編集</p>
      <form class="edit-form" v-on:submit.prevent="doUpdate(editId)">        
        <textarea class="edit-cancel-textarea" ref="comment" cols="30" rows="10" v-model="memos[editId].comment">          
        </textarea>
        <button type="submit">更新</button>
        <!-- <button type="button" v-on:click="editMemoData.edit = false">キャンセル</button>
        <button type="button" v-on:click="doRemove(editMemoData)">削除</button> -->
        <button class="edit-cancel-bottun" type="button" v-on:click="notEdit(editMemoData)">キャンセル</button>  
      </form>
    </div>
  </div>
</template>

<script>
const STORAGE_KEY = 'memo_app_vue'
const memostorage = {
  fetch: function () {
    const memos = JSON.parse(
      localStorage.getItem(STORAGE_KEY) || '[]'
    )
    memos.forEach(function (todo, index) {
      todo.id = index
    })
    memostorage.uid = memos.length
    return memos
  },
  save: function (memos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(memos))
  }
}

export default {
  name: 'indexMemo',
  data() {
    return{
      memos: [],
      createMemoVisible: false,
      editMemoVisible: false,
      editMemoData: null,
      editId: null,
    }
  },
  created () {
    this.memos = memostorage.fetch()
  },
  methods: {
    doAdd: function () {
      const comment = this.$refs.comment
      if (!comment.value.length) {
        return
      }
      this.memos.push({
        id: memostorage.uid++,
        comment: comment.value,
        edit: false
      })
      comment.value = ''
    },
    doUpdate: function (id) {
      const comment = this.$refs.comment
      if (!comment.value.length) {
        return
      }
      this.memos[id].comment = comment.value
      this.memos[id].edit = false
      this.editMemoVisible = false
      comment.value = ''
    },
    doRemove: function (item) {
      const index = this.memos.indexOf(item)
      this.memos.splice(index, 1)
    },
    doEdit: function (item) {
      const index = this.memos.indexOf(item)
      this.editMemoData = this.memos[index]
      this.editId = index
      item.edit = true
      this.editMemoVisible = true
    },
    notEdit: function (item) {
      item.edit = false
      this.editMemoVisible = false
    }
  },
  watch: {
    memos: {
      handler: function (memos) {
        memostorage.save(memos)
      },
      deep: true
    }
  }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#indexMemo {
  display:flex;
  flex-wrap: nowrap;
  width:100%;
}
.indexMemo-main-container {
  height: auto;
  width: 30%;
  display: inline-block;
}
.index-memo ul {
  list-style: none;
  padding-left: 0;
}

.add-form {
  display: inline-block;
}

.add-form textarea{
  width: 100%;
}

.indexMemo-edit-container{  
  height: auto;
  width: 60%;
  display: inline-block;
}

</style>
