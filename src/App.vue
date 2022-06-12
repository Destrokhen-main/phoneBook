<template>
  <div class="screen">
    <div class="container">
      <div class="block">
        <div style="padding: 10px">
          <div class="create-block">
            <button
              class="button-primary"
              @click="save"
              style="margin-right: 10px"
            >Сохранить</button>
            <button class="button-primary" @click="FuncShowModal">Создать</button>
          </div>
          <div>
            <div class="header">
              <div class="header-b1" @click="sortedAlgorithm('name')">Имя {{sort.name ? '⟱' : '⟰'}}</div>
              <div class="header-b2" @click="sortedAlgorithm('phoneNoFormat')">Телефон {{sort.phoneNoFormat ? '⟱' : '⟰'}}</div>
            </div>
          </div>
          <ItemTable
            v-for="(parent, index) in listUsers"
            :key="`${parent.id}-${parent.child.length}`"
            :parent="parent"
            :index="index"
          />
          <div v-if="listUsers.length == 0">
            <p>Лист пуст</p>
          </div>
        </div>
      </div>
    </div>
    <ModalWindow
      v-if="showModal"
      @close="FuncCloseModal"
      @create="createNewUser"
      :parents="parents"
    />
  </div>
</template>

<script>
import ModalWindow from './components/modal.vue'
import ItemTable from './components/tableItem.vue'

export default {
  name: 'App',
  data () {
    return {
      showModal: false,
      listUsers: [],
      parents: [],
      sort: {
        name: true,
        phoneNoFormat: true
      }
    }
  },
  components: {
    ModalWindow,
    ItemTable
  },
  beforeDestroy () {
    // Должно работать, но не работает (((
    if (this.listUsers.length > 0) {
      localStorage.setItem('ListUsers', JSON.stringify(this.listUsers))
    }
  },
  mounted () {
    const list = localStorage.getItem('ListUsers')
    if (list !== '' && list !== null) {
      this.listUsers = JSON.parse(list)
      this.getAllParent()
    }
  },
  methods: {
    save () {
      if (this.listUsers.length > 0) {
        localStorage.setItem('ListUsers', JSON.stringify(this.listUsers))
      }
    },
    FuncShowModal () {
      this.showModal = true
    },
    FuncCloseModal () {
      this.showModal = false
    },
    createNewUser (obj) {
      let id
      let listUsers = this.listUsers
      let pathToList = ''

      if (obj.parent === '-1') {
        id = listUsers.length
        listUsers.push({
          ...obj,
          id,
          showChild: false,
          child: []
        })
      } else {
        const req = (id, listUser, item, newId, path) => {
          let newListUser = []
          let find = false

          for (let i = 0; i !== listUser.length; i++) {
            const newPathToUser = (path !== '' ? '/' : '')

            if (listUser[i].id === id) {
              item.id = listUser[i].id + '-' + listUser[i].child.length
              newId = item.id
              path += newPathToUser + listUser[i].name

              listUser[i].child.push(item)
              newListUser.push(listUser[i])
              find = true
              continue
            }

            if (!find && listUser[i].child.length > 0) {
              const pathToUser = path + newPathToUser + listUser[i].name;
              [listUser[i].child, newId, path] = req(id, listUser[i].child, item, newId, pathToUser)
              newListUser.push(listUser[i])
            } else {
              newListUser.push(listUser[i])
            }
          }
          return [newListUser, newId, path]
        }

        const object = {
          ...obj,
          showChild: false,
          child: []
        }
        let [NewListUsers, newId, path] = req(obj.parent, listUsers, object, '', '')
        id = newId
        listUsers = NewListUsers
        pathToList = path
      }
      const formatPathToList = (pathToList !== '' ? pathToList + ' | ' : '')
      this.parents.push({
        id,
        name: formatPathToList + obj.name
      })
    },
    getAllParent () {
      const req = (list, ar, path) => {
        for (let i = 0; i !== list.length; i++) {
          ar.push({
            id: list[i].id,
            name: path + (path !== '' ? ' | ' : '') + list[i].name
          })

          if (list[i].child.length > 0) {
            req(list[i].child, ar, path + (path !== '' ? '/' : '') + list[i].name)
          }
        }
        return ar
      }

      this.parents = req(this.listUsers, [], '')
    },
    async sortedAlgorithm (key) {
      const sortedReq = (list, check) => {
        for (let i = 0; i !== list.length; i++) {
          if (list[i].child.length > 0) {
            list[i].child = sortedReq(list[i].child, check)
          }
        }
        return list.sort(check)
      }

      if (this.sort[key]) {
        this.listUsers = await sortedReq(this.listUsers, (a, b) => b[key].localeCompare(a[key]))
        this.sort[key] = false
      } else {
        this.listUsers = await sortedReq(this.listUsers, (a, b) => a[key].localeCompare(b[key]))
        this.sort[key] = true
      }
    }
  }
}
</script>

<style>
  body {
    background-color: #efefef;
  }

  .container {
    display: flex;
    justify-content: center;
  }

  .block {
    width: 1000px;
    min-height: 500px;
    background-color:#ffffff;
    border-radius: 10px;
  }

  .create-block {
    display: flex;
    justify-content: end;
  }

  .button-primary {
    border-radius: 10px;
    padding: 5px 10px;
    border: 2px solid grey;
    transition: 0.3s;
    cursor: pointer;
  }

  .table-block {
    display: flex;
    justify-content:center;
    flex-wrap: wrap;
    margin-top: 10px;
  }

  .button-primary:hover {
    background-color: gainsboro;
  }

  .table__item {
    width: 100%;
  }

  .header {
    display: flex;
    width: 100%;
    border: 1px solid black;
    margin-top: 10px;
    background-color: #d1d7ff;
  }
  .header-b1 {
    width: 100%;
    text-align: center;
    cursor: pointer;
    padding: 10px 0;
  }
  .header-b2 {
    width: 30%;
    text-align: center;
    cursor: pointer;
    padding: 10px 0;
  }
</style>
