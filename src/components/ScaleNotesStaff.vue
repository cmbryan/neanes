<template>
  <div class="scale-notes-staff-container">
    <svg :width="width" :height="height">
      <!-- Treble Clef (if first note) -->
      <image
        v-if="isFirstLineElement"
        :x="0"
        :y="lineY(1) - 14"
        height="46"
        href="@/assets/icons/western_notation/treble_clef.svg"
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
import { ScaleNote } from '@/models/Scales';
import { NoteAtomNode } from '@/services/audio/AnalysisService';

@Component
export default class ScaleNotesStaff extends Vue {
  @Prop({ required: true }) noteAtomNodes!: NoteAtomNode[];
  @Prop({ required: true }) isFirstLineElement!: boolean;
  @Prop({ required: true }) pageSetup!: PageSetup;

  lineWidth = 1;
  noteRadius = 3;
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
    return this.lineSpacing * line;
  }

  positionY(index: number) {
    // Calculate the Y position of a note: 6 is the middle line, measured from the top
    return this.lineSpacing * index * 0.5;
  }

  noteX(index: number) {
    // Calculate the X position of a note
    return this.noteRadius + (index + 1) * 15 + 20;
  }

  noteY(note: ScaleNote) {
    // Calculate the Y position of a note based on its scale degree
    switch (note) {
      case ScaleNote.ZoLow:
        return this.positionY(20);
      case ScaleNote.NiLow:
        return this.positionY(19);
      case ScaleNote.PaLow:
        return this.positionY(18);
      case ScaleNote.VouLow:
        return this.positionY(17);
      case ScaleNote.GaLow:
        return this.positionY(16);
      case ScaleNote.ThiLow:
        return this.positionY(15);
      case ScaleNote.KeLow:
        return this.positionY(14);
      case ScaleNote.Zo:
        return this.positionY(13);
      case ScaleNote.Ni:
        return this.positionY(12);
      case ScaleNote.Pa:
        return this.positionY(11);
      case ScaleNote.Vou:
        return this.positionY(10);
      case ScaleNote.Ga:
        return this.positionY(9);
      case ScaleNote.Thi:
        return this.positionY(8);
      case ScaleNote.Ke:
        return this.positionY(7);
      case ScaleNote.ZoHigh:
        return this.positionY(6);
      case ScaleNote.NiHigh:
        return this.positionY(5);
      case ScaleNote.PaHigh:
        return this.positionY(4);
      case ScaleNote.VouHigh:
        return this.positionY(3);
      case ScaleNote.GaHigh:
        return this.positionY(2);
      case ScaleNote.ThiHigh:
        return this.positionY(1);
      case ScaleNote.KeHigh:
        return this.positionY(0);
      default:
        return this.positionY(0);
    }
  }

  noteDur(duration: number) {
    const stemLength = 10;
    const flagWidth = 4;
    const flagHeight = 6;
    const stemX = this.noteRadius;
    const stemY = 0;
    const flagX = stemX;
    const flagY = -stemLength;

    switch (duration) {
      case 1:
        return {
          stem: `M ${stemX}, ${stemY} v -${stemLength}`,
          flag: null,
        };
      case 0.5:
        return {
          stem: `M ${stemX}, ${stemY} v -${stemLength}`,
          flag: `M ${flagX}, ${flagY} l ${flagWidth}, ${flagHeight} v -${flagHeight}`,
        };
      default:
        return {
          stem: null,
          flag: null,
        };
    }
  }
}
</script>

<style scoped>
.scale-notes-staff-container {
  width: 100%;
  height: 100%;
}
</style>
