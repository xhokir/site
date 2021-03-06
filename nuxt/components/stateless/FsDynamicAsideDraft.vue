<template>
  <div>
    <v-card class="mt-5">
      <v-card-title class="fs-card-title">{{ $t('info') }}</v-card-title>
      <v-card-text>
        <fs-table-draft-info :draft="draft" />
      </v-card-text>
    </v-card>
    <v-card class="mt-5">
      <v-card-title class="fs-card-title">{{ $t('actions') }}</v-card-title>
      <v-card-text>
        <v-list>
          <v-list-tile :to="$fs.path('/fork/'+draft.handle)" :disabled="typeof draft.data.gist === 'undefined'">
            <v-list-tile-action><v-icon color="success">call_split</v-icon></v-list-tile-action>
            <v-list-tile-content>{{ $t('fork') }}
              <span v-if="typeof draft.data.gist === 'undefined'">({{ $t('upgradeRequired') }})</span>
            </v-list-tile-content>
          </v-list-tile>
          <v-list-tile :to="$fs.path('/redraft/'+draft.handle+'/for/'+draft.model.handle)" :disabled="typeof draft.data.gist === 'undefined'">
            <v-list-tile-action><v-icon color="warning">repeat</v-icon></v-list-tile-action>
            <v-list-tile-content>{{ $t('redraft') }}
              <span v-if="typeof draft.data.gist === 'undefined'">({{ $t('upgradeRequired') }})</span>
            </v-list-tile-content>
          </v-list-tile>
          <v-list-tile @click="upgradeDraft">
            <v-list-tile-action><v-icon color="info">autorenew</v-icon></v-list-tile-action>
            <v-list-tile-content>{{ $t('upgrade') }}</v-list-tile-content>
          </v-list-tile>
          <v-list-tile @click="download = !download">
            <v-list-tile-action>
              <v-icon>archive</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>{{ $t('download') }}
                <v-icon color="secondary" class="ml-1" v-if="download">arrow_drop_up</v-icon>
                <v-icon color="secondary" class="ml-1" v-else>arrow_drop_down</v-icon>
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </v-list>
        <v-list v-if="download" dense>
          <v-list-tile :href="$fs.draftDownloadLink(draft.handle, 'svg')">
            <v-list-tile-action><v-icon color="info">code</v-icon></v-list-tile-action>
            <v-list-tile-content>{{ $t('svgSourceCode') }}</v-list-tile-content>
          </v-list-tile>
          <v-divider></v-divider>
          <v-list-tile v-for="f in iso" :href="$fs.draftDownloadLink(draft.handle, f.format)" :key="f.format">
            <v-list-tile-action><v-icon color="primary">insert_drive_file</v-icon></v-list-tile-action>
            <v-list-tile-content>{{ f.title }}</v-list-tile-content>
          </v-list-tile>
          <v-divider></v-divider>
          <v-list-tile v-for="f in us" :href="$fs.draftDownloadLink(draft.handle, f.format)" :key="f.format">
            <v-list-tile-action><v-icon>insert_drive_file</v-icon></v-list-tile-action>
            <v-list-tile-content>{{ f.title }}</v-list-tile-content>
          </v-list-tile>
        </v-list>
        <v-divider />
          <v-list>
            <v-list-tile
                       v-if="$store.state.actions.deleteDraft === false"
                       @click="$store.commit('setAction', {action: 'deleteDraft', value: draft.handle})" >
                       <v-list-tile-action><v-icon color="error">delete</v-icon></v-list-tile-action>
                       <v-list-tile-content>{{ $t('deleteThisDraft') }}</v-list-tile-content>
            </v-list-tile>
            <v-list-tile v-else @click="$store.commit('setAction', {action: 'deleteDraft', value: false})" >
              <v-list-tile-action><v-icon>cancel</v-icon></v-list-tile-action>
              <v-list-tile-content>{{ $t('cancel') }}</v-list-tile-content>
            </v-list-tile>
          </v-list>
        </v-card-text>

        <v-divider></v-divider>
      </v-card>
  </div>
</template>

<script>
import FsTableDraftInfo from '~/components/stateless/FsTableDraftInfo'
export default {
  name: 'FsDynamicAsideDraft',
  components: {
    FsTableDraftInfo
  },
  computed: {
    draft () {
      return this.$store.state.components.data['draft']
    }
  },
  data: function () {
    return {
      iso: [
      { format: 'a4.pdf', 'title': 'A4 PDF' },
      { format: 'a3.pdf', 'title': 'A3 PDF' },
      { format: 'a2.pdf', 'title': 'A2 PDF' },
      { format: 'a1.pdf', 'title': 'A1 PDF' },
      { format: 'a0.pdf', 'title': 'A0 PDF' },
      { format: 'pdf', 'title': this.$t('fullSizePdf') },
      ],
      us: [
      { format: 'letter', 'title': this.$t('letterPdf') },
      { format: 'tabloid', 'title': this.$t('tabloidPdf') },
      ],
      download: false
    }
  },
  methods: {
    upgradeDraft() {
      this.$fs.upgradeDraft(this.draft.handle)
      .then((result) => {
        // fixme: This is hackish
        window.location.reload(true)
      })
      .catch((error) => {
        this.error = true
      })
    },
  }
}
</script>
