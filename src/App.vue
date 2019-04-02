<script>
const months = [
  'January',
  'February',
  'March',
  'April',
  'May',
  'June',
  'July',
  'August',
  'September',
  'October',
  'November',
  'December',
]

export default {
  data() {
    return {
      meetup: {
        type: 'meetup',
        startDate: '2019-04-01',
        endDate: '2019-04-01',
        time: '18:00'
      },
      current: null,
      error: null,
    }
  },

  computed: {
    json() {
      /** @type {Date} */
      const startDate = new Date(`${this.meetup.startDate} ${this.meetup.time}`)
      const date = startDate.getDate()
      const dateStr = `${date}${
        date % 10 === 1
          ? 'st'
          : date % 10 === 2
          ? 'nd'
          : date % 10 === 3
          ? 'rd'
          : 'th'
      }`
      const timeStr = `at ${startDate.toTimeString()}`

      return {
        ...this.meetup,
        date: dateStr,
        time: timeStr
      }
    },
    jsonStr() {
      return JSON.stringify(this.json, null, 2)
    },
    fullStr() {
      if (!this.current) return

      const startDate = new Date(`${this.meetup.startDate} ${this.meetup.time}`)
      const month = months[startDate.getMonth()]
      const copy = JSON.parse(
        JSON.stringify(this.current)
      )

      copy[month] = copy[month] || []

      copy[month].push(this.json)

      return JSON.stringify(copy, null, 2)
    }
  },

  methods: {
    copyToClipboard() {
      const el = this.$refs.textarea;
      el.select();
      document.execCommand('copy');
    },
  },

  async created() {
    const response = await fetch(
      'https://raw.githubusercontent.com/vuejs/events/master/src/.vuepress/data/2019.json'
    )

    if (response.ok) {
      this.current = await response.json()
    } else {
      this.error = response.statusText
    }
  },
}
</script>

<template>
  <div class="container" @submit.prevent="copyToClipboard">
    <main>
      <form>
        <label>
          Event Type
          <select v-model="meetup.type">
            <option value="conference">Conference</option>
            <option value="meetup">Meetup</option>
            <option value="workshop">Workshop</option>
            <option value="other">Other</option>
          </select>
        </label>

        <label>
          Event Name
          <input v-model="meetup.name">
        </label>

        <label>
          Start Date
          <input v-model="meetup.startDate" type="date">
        </label>
        <label>
          Start Time
          <input v-model="meetup.time" type="time">
        </label>

        <label>
          End Date
          <input v-model="meetup.endDate" type="date">
        </label>

        <label>
          Organizer
          <input v-model="meetup.organiser">
        </label>
        <label>
          Organizer Website
          <input v-model="meetup.organiserLink" type="url">
        </label>
        <label>
          Event Website
          <input v-model="meetup.eventLink" type="url">
        </label>

        <button type="submit">Copy to Clipboard</button>
      </form>

      <pre><code>{{ jsonStr }}</code></pre>

      <h3><a href="https://github.com/vuejs/events/edit/master/src/.vuepress/data/2019.json" target="_blank" rel="noopener noreferrer">Create PR</a> with following event.json</h3>
      <textarea v-if="this.current" :value="fullStr" ref="textarea" />
      <div v-else>Fetching latest version of events.json</div>
    </main>

    <footer>
      Built with Vue | <a href="https://github.com/znck/dirty-vue-meetup-edit" target="_blank" rel="noopener noreferrer">Contribute/Clone</a>
    </footer>
  </div>
</template>

<style>
html {
  font-size: 16px;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
    Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
}

.container {
  margin: 40px auto;
  max-width: 600px;
}

form {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
}

label {
  box-sizing: border-box;
  display: block;
  flex: 1 1 50%;
  padding: 0 1rem 0.5rem;
}

@media screen and (max-width: 600px) {
  label {
    flex: none;
    width: 100%;
  }
}

select,
input {
  display: block;
  font-size: 1rem;
  line-height: 1.5rem;
  width: 100%;
  margin-top: 0.25rem;
}

button {
  margin: 2rem auto;
  font-size: 1rem;
  line-height: 1.5rem;
}

pre {
  display: block;
}

textarea {
  width: 100%;
  height: 200px;
}

footer {
  margin-top: 2rem;
  text-align: center;
  font-size: 0.75rem;
}
</style>