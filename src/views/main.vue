<template>
    <h1>Celebrity Impersonator</h1>
    <h4>Ask your favorite celebrity a question!</h4>
    <br><br>

    <div class="btn-group">
        <button class="btn btn-primary dropdown-toggle" type="button" id="defaultDropdown" data-bs-toggle="dropdown" data-bs-auto-close="true" aria-expanded="false">
            {{ celeb }}
        </button>
        <ul class="dropdown-menu" aria-labelledby="defaultDropdown">
            <a class="dropdown-item" @click="celeb='Elon Musk'">Elon Musk</a>
            <a class="dropdown-item" @click="celeb='Kanye West'">Kanye West</a>
            <a class="dropdown-item" @click="celeb='Barrack Obama'">Barrack Obama</a>
            <hr class="dropdown-divider">
            <input v-model.lazy="celeb" id="input" type="text" placeholder="Enter Celeb">

        </ul>
    </div>
    <button @click="ask()" id="ask" class="btn btn-primary">Ask</button>

    <br><br>

    <div class="form-outline">
        <textarea v-model="question" class="form-control" id="textAreaExample2" rows="6" :placeholder="'Ask ' + [[ celeb ]] + ' a question'"></textarea>
        <label class="form-label" for="textAreaExample2"></label>
    </div>

    <div class="form-outline">
    <textarea v-model="answer" class="form-control" id="textAreaExample3" rows="3" :placeholder="[[ celeb ]] + 's response'"></textarea>
    </div>

    <br><br>
    <!-- Button trigger modal -->
    <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
    View History
    </button>

    <!-- Modal -->
    <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-xl">
        <div class="modal-content">
        <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">Question History</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
            <div class="modal-body">
                  <table
                    class="table table-bordered table-hover table-sm mx-auto"
                  >
                    <thead>
                      <tr>
                        <th style="width: 16.66%">Celebrity</th>
                        <th style="width: 33%">User Question</th>
                        <th>Celebrity Answer</th>
                      </tr>
                    </thead>
                    <tbody>
                        <tr
                            v-for="query in queries"
                            :key="query.q"
                        >
                        <td>
                            <input id="inp1"
                            type="text"
                            class="form-control-plaintext"
                            :value="query.c"
                            readonly
                          />
                        </td>
                         <td>
                            <input id="inp2"
                            type="text"
                            class="form-control-plaintext"
                            :value="query.q"
                            readonly
                          />
                        </td>
                         <td>
                            <input id="inp3"
                            type="text"
                            class="form-control-plaintext"
                            :value="query.a"
                            readonly
                          />
                        </td>
                        
                        </tr>

                    </tbody>
                  </table>
            </div>
        </div>
        <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        </div>
        </div>
    </div>
    </div>
</template>

<script>

export default {
    data() {
        return {
            celeb: 'Elon Musk',
            question: '',
            answer: '',
            query: {},
            queries: JSON.parse(localStorage.getItem("data")),
            data: {
                "prompt": "",
                "max_tokens": 5,
                "temperature": 1,
                "top_p": 1,
                "n": 1,
                "stream": false,
                "logprobs": null,
                "stop": "\n"
            }
        }
    },
    methods: {
        ask() {
            let p = "Answer the following question as " + this.celeb + "\n" + "Question: " + this.question

            this.query = {
                c: this.celeb,
                q: this.question,
                a: ''
            }

            const data = {
                prompt: p,
                temperature: 0.5,
                max_tokens: 64,
                top_p: 1.0,
                frequency_penalty: 0.0,
                presence_penalty: 0.0,
            };

            fetch("https://api.openai.com/v1/engines/text-curie-001/completions", {
                method: "POST",
                headers: {
                "Content-Type": "application/json",
                Authorization: `Bearer ${import.meta.env.VITE_OPENAI_SECRET}`,
                },
                body: JSON.stringify(data),
            })
            .then((response) => response.json())
            .then((r) => {
                this.answer = r.choices[0].text.trim()
                this.query.a = this.answer
                if (this.queries == null) {
                    this.queries = []
                }
                this.queries.unshift(this.query)
                localStorage.setItem("data", JSON.stringify(this.queries))

            })
            .catch(error => {
                console.error(error);
            })
            console.clear()
        }
    }

}

</script>

<style scoped>
 .form-outline {
     width: 60%;
     margin: auto;
 }

 .btn-group {
     float: left;
     margin-left: 20%;
 }

 #ask {
     float: right;
     margin-right: 20%;
 }

 #input {
     width: 80%;
     margin-left: 10%;
 }

 #inp1 #inp2 #inp3 {
     text-align: center;
 }


</style>