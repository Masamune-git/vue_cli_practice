<template>
    <div id="index-memo">    
    <div class="index-memo-main-container">      
      <div class="index-memo">
        <h2>一覧</h2>
        <div v-if="editMemoVisible">
          <ul  
            v-for="memo in memos"
            :key="memo.id"
          >
            <li >
              <a v-show="memo.edit" @click.prevent="editCancel(memo)">
                {{ memo.comment.split('\n')[0] }}
              </a>
              <p v-show="!memo.edit">{{ memo.comment.split('\n')[0] }}</p>
            </li>
          </ul>
        </div>
        <div v-else>
          <ul 
            v-for="memo in memos"
            :key="memo.id"
          >        
            <li >
              <a v-show="!memo.edit" @click.prevent="doEdit(memo)">
                {{ memo.comment.split('\n')[0] }}
              </a>
            </li>
          </ul>
        </div>        
      </div>
      <h2>メモの追加</h2>
      <div class="create-memo" v-show="createMemoVisible">        
        <form class="add-form" @submit.prevent="doAdd">
          <textarea ref="comment" cols="30" rows="10"></textarea>
          <button type="submit" @click="createMemoVisible = !createMemoVisible">追加</button>
          <button type="button" @click="createMemoVisible = !createMemoVisible">キャンセル</button>
        </form>
      </div>
      <a v-show="!createMemoVisible" @click.prevent="createMemoVisible = !createMemoVisible">+</a>
    </div>
    <div v-if="editMemoVisible" class="index-memo-edit-container">
      
      <p>メモの編集</p>
      <form class="edit-form" @submit.prevent="doUpdate(editMemoData)">        
        <textarea class="edit-cancel-textarea" ref="comment" cols="30" rows="10" v-model="beforeComment">          
        </textarea>
        <button type="submit">更新</button>        
        <button type="button" @click="doRemove(editMemoData)">削除</button>
        <button class="edit-cancel-bottun" type="button" @click="editCancel(editMemoData)">キャンセル</button>  
      </form>
    </div>
  </div>
</template>

<script>
const STORAGE_KEY = 'memo_app_vue'
const memostorage = {
  fetch () {
    const memos = JSON.parse(
      localStorage.getItem(STORAGE_KEY) || '[]'
    )
    memos.forEach(function (todo, index) {
      todo.id = index
    })
    memostorage.uid = memos.length
    return memos
  },
  save (memos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(memos))
  }
}

export default {
  name: 'index-memo',
  data() {
    return{
      memos: [],
      createMemoVisible: false,
      editMemoVisible: false,
      editMemoData: null,
      beforeComment: null,
      editId: null
    }
  },
  created () {
    this.memos = memostorage.fetch()
  },
  methods: {
    doAdd () {
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
    doUpdate (memo) {
      const comment = this.$refs.comment
      const index = this.memos.indexOf(memo)
      this.editMemoData = this.memos[index]
      if (!comment.value.length) {
        return
      }
      this.editMemoData.comment = comment.value
      this.editMemoData.edit = false
      this.editMemoVisible = false
      comment.value = ''
    },
    doRemove (memo) {
      const index = this.memos.indexOf(memo)
      this.memos.splice(index, 1)
      this.editMemoVisible = false
    },
    doEdit (memo) {
      const index = this.memos.indexOf(memo)
      this.editMemoData = this.memos[index]
      this.beforeComment = this.memos[index].comment
      this.editId = index
      memo.edit = true
      this.editMemoVisible = true
    },
    editCancel (memo) {
      memo.edit = false
      this.editMemoVisible = false
    }
  },
  watch: {
    memos: {
      handler (memos) {
        memostorage.save(memos)
      },
      deep: true
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
a {
  color: blueviolet;
  text-decoration: underline;
  cursor: pointer; 
}
button {
  border-radius:5px;
  margin: 10px;
  cursor: pointer; 
}
button:hover {
  opacity: 0.5 ;
}
#index-memo {
  display:flex;
  flex-wrap: nowrap;
  width:100%;
}
.index-memo-main-container {
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
.index-memo-edit-container{  
  height: auto;
  width: 60%;
  display: inline-block;
}
.edit-cancel-textarea {
  margin: 0 100% 0 0;
}
</style>
