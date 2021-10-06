<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Example Component</div>

                    <div class="card-body">
                        <file-pond
                          name="photo"
                          ref="pond"
                          label-idle="Drop files here..."
                          allow-multiple="false"
                          :accepted-file-types="['image/*']"
                          max-files="1"
                          :files="files"
                          :server="serverOptions"
                        />
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import vueFilePond from 'vue-filepond';

import FilePondPluginFileValidateType from 'filepond-plugin-file-validate-type/dist/filepond-plugin-file-validate-type.esm.js';
import FilePondPluginImagePreview from 'filepond-plugin-image-preview/dist/filepond-plugin-image-preview.esm.js';

import 'filepond/dist/filepond.min.css';
import 'filepond-plugin-image-preview/dist/filepond-plugin-image-preview.min.css';

const FilePond = vueFilePond( FilePondPluginFileValidateType, FilePondPluginImagePreview );

export default {
  name: 'app',
  data: function() {
    return {
      serverOptions: {
        process: (fieldName, file, metadata, load, error) => {
          const formData = new FormData();
          formData.append(fieldName, file, file.name);
          axios({
            method: "POST",
            url: '/chat/room/upload',
            data: formData,
            headers: {
              "Content-Type": "multipart/form-data"
            }
          })
          .then(() => {
            load();
          })
          .catch(() => {
            error();
          });
        }
      },
      files: [],
    };
  },
  methods: {
    handleFilePondInit: function () {
      console.log('FilePond has initialized');

      // example of instance method call on pond reference
      this.$refs.pond.getFiles();
      console.log(this.$refs.pond.getFiles());
    },
  },
  components: {
    FilePond
  },
};
</script>
