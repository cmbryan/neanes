<template>
  <div class="scale-notes-staff-container" :style="{ height: `${height-25}px` }">
    <svg :width="width">
      <!-- Treble Clef (if first note) -->
      <image
        v-if="isFirstLineElement"
        :x="0"
        :y="lineY(1) - 6"
        height="30"
        href="@/assets/icons/western_notation/GClef.svg"
      />

      <!-- Staff Lines -->
      <line
        v-for="i in 5"
        :key="`staff-line-${i}`"
        :x1="0"
        :y1="lineY(i)"
        :x2="width"
        :y2="lineY(i)"
        stroke="black"
        :stroke-width="lineWidth"
      />

      <!-- Notes -->
      <template
        v-for="(noteAtomNode, index) in noteAtomNodes"
        :key="`note-${index}`"
      >
        <g
          :transform="`translate(${noteX(index)}, ${noteY(noteAtomNode.physicalNote)})`"
        >
          <circle :r="noteRadius" fill="black" />
        </g>
      </template>
    </svg>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-facing-decorator';

import { PageSetup } from '@/models/PageSetup';
import { getScaleNoteValue, ScaleNote } from '@/models/Scales';
import { NoteAtomNode } from '@/services/audio/AnalysisService';

@Component
export default class ScaleNotesStaff extends Vue {
  @Prop({ required: true }) noteAtomNodes!: NoteAtomNode[];
  @Prop({ required: true }) isFirstLineElement!: boolean;
  @Prop({ required: true }) pageSetup!: PageSetup;

  lineWidth = 1;
  noteRadius = 2.4;
  lineSpacing = 4;
  width = 80; // Default width, will be updated dynamically
  height = this.lineSpacing * 5 + 20; // Fixed height for the staff

  mounted() {
    this.updateWidth();
  }

  @Watch('pageSetup', { deep: true })
  onPageSetupChanged() {
    this.updateWidth();
  }

  @Watch('noteAtomNodes')
  onScaleNotesChanged() {
    this.updateWidth();
  }

  updateWidth() {
    // Calculate width based on the number of notes
    this.width = Math.max(80, this.noteAtomNodes.length * 15);
    this.width = 80;
  }

  lineY(line: number) {
    // Calculate the Y position of a staff line
    return this.lineSpacing * line + 3;
  }

  noteX(index: number) {
    // Calculate the X position of a note
    return this.noteRadius + (index + 1) * 15 + 20;
  }

  noteY(note: ScaleNote) {
    // Calculate the Y position of a note based on its scale degree
    return this.lineSpacing * getScaleNoteValue(note) * -0.5 + 25.1;
  }

}
</script>

<style scoped>
.scale-notes-staff-container {
  width: 100%;
  height: 100%;
}
</style>
