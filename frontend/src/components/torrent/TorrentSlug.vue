<template>
  <template v-for="(part, partIndex) in computedSlug" :key="partIndex">
    <template v-if="part.length > 0">
      <span v-for="(item, itemIndex) in part" :key="itemIndex">
        <span class="slash" v-if="itemIndex > 0 || (partIndex > 0 && computedSlug[partIndex].length > 0)"> / </span>
        <span :class="{ bold: partIndex === 1 }">{{ item }}</span>
      </span>
    </template>
  </template>
  <!-- <span v-if="'peer_status' in torrent && torrent.peer_status !== null">
    <span class="slash"> / </span> <span :class="torrent.peer_status">{{ torrent.peer_status }}</span>
  </span> -->
</template>

<script lang="ts" setup>
import type { ContentType, EditionGroupInfoLite, TorrentHierarchyLite } from '@/services/api/torrentService'
import { useI18n } from 'vue-i18n'
import { computed } from 'vue'

const { t } = useI18n()

const props = defineProps<{
  torrent: TorrentHierarchyLite
  editionGroup?: EditionGroupInfoLite
  contentType: ContentType
}>()

const computedSlug = computed<string[][]>(() => {
  const firstPart: string[] = []
  const features: string[] = []
  const releaseGroup: string[] = []

  if (props.torrent.video_resolution) {
    if (props.torrent.video_resolution === 'Other' && 'video_resolution_other_x' in props.torrent && 'video_resolution_other_y' in props.torrent) {
      firstPart.push(`${props.torrent.video_resolution_other_x as number}x${props.torrent.video_resolution_other_y as number}`)
    } else {
      firstPart.push(props.torrent.video_resolution)
    }
  }
  if (props.editionGroup?.source) {
    firstPart.push(props.editionGroup.source)
  }
  if (props.torrent.video_codec) {
    firstPart.push(props.torrent.video_codec)
  }
  if (props.editionGroup?.name) {
    firstPart.push(props.editionGroup.name)
  }
  if (props.torrent.container && props.contentType !== 'music') {
    firstPart.push(props.torrent.container)
  }
  if (props.torrent.audio_codec) {
    firstPart.push(props.torrent.audio_codec)
  }
  if (props.torrent.audio_channels) {
    firstPart.push(props.torrent.audio_channels)
  }
  if (props.torrent.audio_bitrate_sampling) {
    firstPart.push(props.torrent.audio_bitrate_sampling)
  }
  if (props.torrent.languages.length === 1 && props.torrent.languages[0] !== 'English') {
    firstPart.push(props.torrent.languages[0])
  }
  if (props.torrent.languages.length > 1) {
    firstPart.push(t('torrent.multi_language'))
  }
  if ('trumpable' in props.torrent && props.torrent.trumpable !== '') {
    firstPart.push(t('torrent.trumpable'))
  }
  if ('reports' in props.torrent && props.torrent.reports.length !== 0) {
    firstPart.push(t('general.reported'))
  }

  if (props.torrent.features) {
    props.torrent.features.forEach((feature) => features.push(feature))
  }

  if (props.torrent.release_group) {
    releaseGroup.push(props.torrent.release_group)
  }

  return [firstPart, features, releaseGroup]
})
</script>
<style scoped>
.slash {
  font-weight: 300;
}
.seeding {
  color: green;
}
.leeching {
  color: yellow;
}
.snatched {
  color: white;
}
</style>
