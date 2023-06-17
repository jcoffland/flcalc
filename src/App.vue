<!--

                  This file is part of the Flight Level Calcualtor.

         flcalc computes flight levels from pressure and ground alititude.
                      Copyright (c) 2023, Joseph Coffland
                               All rights reserved.

       This program is free software; you can redistribute it and/or modify
       it under the terms of the GNU General Public License as published by
        the Free Software Foundation; either version 3 of the License, or
                       (at your option) any later version.

         This program is distributed in the hope that it will be useful,
          but WITHOUT ANY WARRANTY; without even the implied warranty of
          MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
                   GNU General Public License for more details.

     You should have received a copy of the GNU General Public License along
     with this program; if not, write to the Free Software Foundation, Inc.,
           51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

                  For information regarding this software email:
                                 Joseph Coffland
                          joseph@cauldrondevelopment.com

-->

<script>
function pressure_offset(pressure) {
  return 44308 * (Math.pow(pressure / 1013.25, 0.190284) - 1)
}


function flight_level(level, ground, pressure) {
  let x = level + ground + pressure_offset(pressure)
  return Math.round(x).toLocaleString() + 'm'
}


export default {
  data() {
    return {
      level: 65,
      ground: 80,
      pressure: 1013
    }
  },


  computed: {
    rows() {
      let rows = []

      let levels = []
      for (let i = 0; i < 11; i++) {
        let level = this.level + 10 * (i - 5)
        levels.push(level)
      }

      rows.push([''].concat(levels).concat(['Offset']))

      for (let i = 0; i < 11; i++) {
        let row = []
        let pressure = this.pressure + (i - 5)
        row.push(pressure.toLocaleString() + 'hpa')

        for (let j = 0; j < 11; j++)
          row.push(flight_level(levels[j] * 30.48, this.ground, pressure))

        row.push(Math.round(pressure_offset(pressure)) + 'm')

        rows.push(row)
      }

      for (let i = 0; i < 11; i++)
        rows[0][i + 1] = 'FL' + rows[0][i + 1]

      return rows
    }
  },


  methods: {
  }
}
</script>

<template lang="pug">
.flcalc
  h1 Flight Level Calculator
  strong (Below transition level)

  fieldset
    label.label Flight Level
    input(type="number", v-model="level")
    label.unit 100s of feet

    label.label Ground Level
    input(type="number", v-model="ground")
    label.unit meters

    label.label Sea Level Pressure
    input(type="number", v-model="pressure")
    label.unit hpa

  table.results
    tr(v-for="row in rows")
      td(v-for="e in row") {{e}}

  p height = ground + flight_level + offset
  p offset = 44308 * ((pressure / 1013.25) ^ 0.190284 - 1)
</template>

<style lang="stylus">
*
  box-sizing border-box

.flcalc
  display flex
  flex-direction column
  gap 1em
  max-width 70em
  margin 0 auto

  > *
    margin 0

  fieldset
    display grid
    grid-template-columns auto auto 1fr
    gap 0.5em
    border none
    padding 0

    label.label
      font-weight bold

    input
      max-width 5em

  table.results
    border-collapse collapse

    th, td
      border 1px solid #222
      padding 0.125em
      text-align right
      font-family mono
      min-width 5em

    tr:nth-child(7), tr > :nth-child(7)
      background #ddd

    tr:nth-child(7) > :nth-child(7)
      background #aaa

    tr:first-child > *, tr > :first-child
      font-weight bold
      background #666
      color #eee
</style>
