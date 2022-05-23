<template>
  <div class="flex flex-col items-center blog">
    <h1 class="text-2xl">BLOG</h1>
    <template v-if="!isUser">
      <template v-if="!isLogin">
        <button @click="isLogin = !isLogin">login</button>
      </template>
      <template v-if="isLogin">
        <template v-if="isNewPassword">
          <input
            type="password"
            placeholder="password"
            v-model="firstPassword"
          />
          <input
            type="password"
            placeholder="repeat password"
            v-model="secondPassword"
          />
          <input
            type="text"
            placeholder="name"
            v-model="newName"
            @keydown.enter="addPassword"
          />
          <div class="flex gap-1 mt-1">
            <button @click="isLogin = !isLogin">Cancel</button>
            <button @click="addPassword">Add password</button>
          </div>
        </template>
        <template v-else>
          <input
            placeholder="password"
            type="password"
            v-model="password"
            @keydown.enter="autoriz"
          />
          <button @click="autoriz" class="mt-1">login</button>
        </template>
      </template>
    </template>
    <div class="flex flex-col mt-5">
      <template v-if="isUser">
        <div>
          <p>Your name: {{ user.name }}</p>
          <button @click="deleteAcc">Delete account</button>
        </div>
      </template>
      <div>
        <input placeholder="Search" v-model="filter" class="mt-5 search" />
      </div>
      <div class="mt-5 w-100">
        <template v-if="!isAdding && isUser">
          <button @click="isAdding = !isAdding">Add</button>
        </template>
        <template v-if="isAdding"
          ><div class="flex flex-col">
            <input
              type="text"
              placeholder="Name"
              v-model="newPublication.header"
            />
            <textarea
              id="newPublicationText"
              type="text"
              placeholder="Text"
              v-model="newPublication.text"
            ></textarea>
            <p>author: {{ user.name }}</p>
          </div>
          <div class="flex flex-row gap-1 mt-1">
            <button @click="isAdding = !isAdding">Cancel</button>
            <button @click="addNewPublication">Add</button>
          </div>
        </template>
      </div>
      <MyPublication
        v-for="(publication, idx) in filteredPublications"
        :key="idx"
        :publication="publication"
        :idx="idx"
        :isUser="isUser"
        @delete-publication="deletePublication"
        @change-publication="changePublication"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',

  mounted() {
    const publicationData = localStorage.getItem('publications-list')
    if (publicationData) {
      this.publications = JSON.parse(publicationData)
    }
    const userData = localStorage.getItem('user-data')
    console.log(userData)
    if (userData) {
      this.user = JSON.parse(userData)
    } else {
      this.isNewPassword = true
    }
  },

  data() {
    return {
      isAdding: false,
      isLogin: false,
      isNewPassword: false,
      newName: '',
      firstPassword: '',
      secondPassword: '',
      user: {
        name: '',
        password: '',
      },
      isUser: false,
      newPublication: {
        header: '',
        text: '',
        author: '',
      },
      publications: [],
      filter: '',
      password: '',
    }
  },

  watch: {
    publications: {
      handler() {
        localStorage.setItem(
          'publications-list',
          JSON.stringify(this.publications)
        )
      },
      deep: true,
    },
  },

  methods: {
    addNewPublication() {
      if (this.newPublication.header !== '') {
        if (this.newPublication.text) {
          this.newPublication.author = this.user.name
          this.publications.unshift(this.newPublication)
          this.newPublication = {
            header: '',
            text: '',
            author: '',
          }
          this.isAdding = false
        } else {
          alert('print text')
        }
      } else {
        alert('print name')
      }
    },
    deletePublication(n) {
      this.publications = this.publications.filter((t, idx) => idx !== n)
    },
    changePublication(n, idx) {
      this.publications[idx] = n
    },
    addPassword() {
      if (this.firstPassword === this.secondPassword) {
        if (this.firstPassword.length < 5) {
          alert("password's length must be more 5 symbols")
        } else if (this.newName !== '') {
            const newUser = {
              name: this.newName,
              password: this.firstPassword,
            }
            localStorage.setItem('user-data', JSON.stringify(newUser))
            this.isUser = true
            this.user = newUser
          } else {
            alert('print name')
          }
      } else {
        alert('diferent passwords')
        this.secondPassword = ''
      }
    },
    autoriz() {
      if (this.user.password === this.password) {
        this.isUser = true
      } else {
        this.password = ''
        alert('wrong password')
      }
    },
    deleteAcc() {
      localStorage.removeItem('user-data', JSON.stringify(this.publications))
      this.isUser = false
      location.reload()
    },
  },

  computed: {
    filteredPublications() {
      return this.publications.filter((publ) =>
        publ.header.toUpperCase().includes(this.filter.toUpperCase())
      )
    },
  },
}
</script>

<style src="./assets/main.css"></style>