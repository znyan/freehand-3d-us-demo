<script>
import Title from './sections/Title.vue'
import Abstract from './sections/Abstract.vue'
import Markdown from './sections/Markdown.vue'
import BibTeX from './sections/BibTeX.vue'
import Carousel from './sections/Carousel.vue'
import Video from './sections/Video.vue'
import ImageSlider from './sections/ImageSlider.vue'
import ImageSliderInner from './sections/ImageSliderInner.vue'
import ImageSelector from './sections/ImageSelector.vue'
import Echart from './sections/Echart.vue'
import Table from './sections/Table.vue'
import Collapse from './sections/Collapse.vue'
import SeletionForComparison from './sections/SeletionForComparison.vue'
import Comment from './sections/Comment.vue'
import Latex from './sections/Latex.vue'
import Model3D from './sections/Model3D.vue'
import GaussianSplats3D from './sections/GaussianSplats3D.vue'

import Introduction from './sections/Introduction.vue'
import ProbeBracket from './sections/ProbeBracket.vue'
import Arm1 from './sections/Arm1.vue'
import Arm2 from './sections/Arm2.vue'
import Arm3 from './sections/Arm3.vue'
import Arm4 from './sections/Arm4.vue'
import Carotid1 from './sections/Carotid1.vue'
import Carotid2 from './sections/Carotid2.vue'
import Carotid3 from './sections/Carotid3.vue'

export default {
  components: {
    Title,
    Introduction,
    ProbeBracket,
    Arm1,
    Arm2,
    Arm3,
    Arm4,
    Carotid1,
    Carotid2,
    Carotid3,
  }
}
</script>


<template>
  <Title/>
  <Introduction/>
  <ProbeBracket/>
  <Arm1/>
  <Arm2/>
  <Arm3/>
  <Arm4/>
  <Carotid1/>
  <Carotid2/>
  <Carotid3/>
</template>