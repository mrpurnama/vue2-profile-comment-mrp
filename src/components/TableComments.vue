<template>
  <div>
    <v-data-table
      :headers="headers"
      :items="comments"
      sort-by="name"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>List Comment</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                New Comment
              </v-btn>
            </template>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-card>
                <v-card-title>
                  <span class="text-h5">{{ formTitle }}</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6">
                        <v-text-field
                          v-model="editedComment.name"
                          label="Name"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6">
                        <v-text-field
                          v-model="editedComment.email"
                          label="E-mail"
                          :rules="emailRules"
                          required
                        ></v-text-field>
                      </v-col>
                      <v-textarea
                        v-model="editedComment.body"
                        label="Content"
                        placeholder="input your content comment here"
                        required
                      ></v-textarea>
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="close">
                    Cancel
                  </v-btn>
                  <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
                </v-card-actions>
              </v-card>
            </v-form>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5"
                >Are you sure you want to delete this comment?</v-card-title
              >
              <v-card-text>Name: {{ editedComment.name }}</v-card-text>
              <v-card-text>E-mail: {{ editedComment.email }}</v-card-text>
              <v-card-text>Content: {{ editedComment.body }}</v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete"
                  >Cancel</v-btn
                >
                <v-btn color="blue darken-1" text @click="deleteItemConfirm(editedComment)"
                  >OK</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
        <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
      </template>
    </v-data-table>
    <v-snackbar
      v-model="snackbar"
      :timeout="!isError && 1000"
      :color="isError ? 'error' : 'success'"
    >
      {{ snackbarMessage }}
      <template v-slot:action="{ attrs }">
        <v-btn text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
export default {
  name: "TableComments",
  data: () => ({
    dialog: false,
    dialogDelete: false,
    valid: true,
    headers: [
      {
        text: "Name",
        align: "start",
        //   sortable: false,
        value: "name",
      },
      { text: "Email", value: "email" },
      { text: "Content", value: "body" },
      { text: "Actions", value: "actions", sortable: false },
    ],
    emailRules: [
      (v) => !!v || "E-mail is required",
      (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
    ],
    comments: [],
    editedIndex: -1,
    editedComment: {
      name: "",
      email: "",
      body: "",
    },
    defaultItem: {
      name: "",
      email: "",
      body: "",
    },
    snackbar: false,
    isError: false,
    snackbarMessage: "",
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Comment" : "Edit Comment";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {
    fetch("https://jsonplaceholder.typicode.com/comments?postId=1")
      .then(async (response) => {
        const data = await response.json();

        // check for error response
        if (!response.ok) {
          // get error message from body or default to response statusText
          const error = (data && data.message) || response.statusText;
          this.openSnackbar(true, response.statusText || 'Something went wrong, please try again later');
          return Promise.reject(error);
        } else {
          this.openSnackbar(false, "Data loaded");
        }

        this.comments = data;
      })
      .catch((error) => {
        this.openSnackbar(true, error || 'Something went wrong, please try again later');
      });
  },

  methods: {
    openSnackbar(isError, message) {
      this.snackbar = true;
      this.snackbarMessage = message;
      this.isError = isError;
    },
    editItem(item) {
      this.editedIndex = this.comments.indexOf(item);
      this.editedComment = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.comments.indexOf(item);
      this.editedComment = item;
      this.dialogDelete = true;
    },

    deleteItemConfirm(item) {
      this.dialogDelete = false;
      fetch(
        "https://jsonplaceholder.typicode.com/comments/" +
          item.id,
        {
          method: "DELETE",
          headers: {
            "Content-type": "application/json; charset=UTF-8",
          },
        }
      )
      .then(async (response) => {
        const data = await response.json();

        // check for error response
        if (!response.ok) {
          // get error message from body or default to response statusText
          const error = (data && data.message) || response.statusText;
          this.openSnackbar(true, response.statusText || 'Something went wrong, please try again later');
          return Promise.reject(error);
        } else {
          this.openSnackbar(false, "Data deleted");
          // this.comments.filter((comment) => comment.id != item.id)
          this.comments.splice(this.comments.indexOf(item), 1)
        }
      })
      .catch((error) => {
        this.openSnackbar(true, error || 'Something went wrong, please try again later');
      });
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedComment = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
    },

    save() {
      if (this.$refs.form.validate()) {
        if (this.editedComment.id) {
          fetch(
            "https://jsonplaceholder.typicode.com/comments/" +
              this.editedComment.id,
            {
              method: "PUT",
              body: JSON.stringify({
                postId: 1,
                name: this.editedComment.name,
                email: this.editedComment.email,
                body: this.editedComment.body,
              }),
              headers: {
                "Content-type": "application/json; charset=UTF-8",
              },
            }
          )
          .then(async (response) => {
            const data = await response.json();

            // check for error response
            if (!response.ok) {
              // get error message from body or default to response statusText
              const error = (data && data.message) || response.statusText;
              this.openSnackbar(true, response.statusText || 'Something went wrong, please try again later');
              return Promise.reject(error);
            } else {
              this.openSnackbar(false, "Data edited");
              Object.assign(this.comments.find((comment) => comment.id == data.id), data)
            }
          })
          .catch((error) => {
            this.openSnackbar(true, error || 'Something went wrong, please try again later');
          });
        } else {
          fetch("https://jsonplaceholder.typicode.com/comments", {
            method: "POST",
            body: JSON.stringify({
              postId: 1,
              name: this.editedComment.name,
              email: this.editedComment.email,
              body: this.editedComment.body,
            }),
            headers: {
              "Content-type": "application/json; charset=UTF-8",
            },
          })
            .then(async (response) => {
              const data = await response.json();

              // check for error response
              if (!response.ok) {
                // get error message from body or default to response statusText
                const error = (data && data.message) || response.statusText;
                this.openSnackbar(true, response.statusText || 'Something went wrong, please try again later');
                return Promise.reject(error);
              } else {
                this.openSnackbar(false, "Data added");
                this.comments = [...this.comments, data]
              }
            })
            .catch((error) => {
              this.openSnackbar(true, error || 'Something went wrong, please try again later');
            });
        }
        this.close();
      }
    },
  },
};
</script>