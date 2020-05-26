<template>
  <div id="app">
    <div id="example-1">
      <button v-on:click="load()">Load</button>
    </div>
    <div class="hor">
      <div id="viz"></div>      
    </div>

  </div>
</template>

<script>
import embed from 'vega-embed'
import axios from 'axios'
import list from './list.js'

export default {
  data(){
    return{
      def: null,
      list: list,
      current:null,
      index:0,
    }
  },
  watch:{
    def(v){
      if(v) this.draw()
    }
  },
  methods:{
    async load(){
     this.def = {
    "$schema": "https://vega.github.io/schema/vega/v5.json",
    "width": 756,
    "padding": 5,
    "config": {
        "axisBand": {
            "bandPosition": 1,
            "tickExtra": true,
            "tickOffset": 0,
            "labelFontSize": 11,
            "labelPadding": 13,
            "tickSize": 120
        }
    },
    "signals": [
        {
            "name": "fields",
            "value": [
                "All Students",
                "Indigenous",
                "Special Needs",
                "BC Residents"
            ]
        },
        {
            "name": "plotWidth",
            "value": 30
        },
        {
            "name": "height",
            "update": "(plotWidth + 10) * length(fields)"
        },
        {
            "name": "selected",
            "value": "",
            "on": [
                {
                    "events": "arc:click!",
                    "update": "datum"
                },
                {
                    "events": "dblclick!",
                    "update": "''"
                }
            ]
        }
    ],
    "title": {
        "text": {
            "signal": "'Student Group               Records'"
        },
        "anchor": "start",
        "frame": "bounds",
        "fontSize": 14,
        "fontWeight": "100",
        "dy": 18,
        "color": "#666666"
    },
    "data": [
        {
            "name": "provlvl",
            "values": [{"district":"Chilliwack","district_number":"033","year":"2017/2018","subpop":"All Students","records":"1077","records2":"1077","value":".82","value2":".82","tooltip":"82%","tooltipR":"       1,077","max":".82","min":".79","percentile_25":".75","percentile_75":".87","min_max_tooltip":"79% - 82%","province_percentile_tooltip":"75% - 87%"},{"district":"Chilliwack","district_number":"033","year":"2017/2018","subpop":"BC Residents","records":"1016","records2":"1016","value":".85","value2":".85","tooltip":"85%","tooltipR":"       1,016","max":".85","min":".8","percentile_25":".8","percentile_75":".91","min_max_tooltip":"80% - 85%","province_percentile_tooltip":"80% - 91%"},{"district":"Chilliwack","district_number":"033","year":"2017/2018","subpop":"Indigenous","records":"178","records2":"178","value":".79","value2":".79","tooltip":"79%","tooltipR":"         178","max":".79","min":".62","percentile_25":".62","percentile_75":".8","min_max_tooltip":"62% - 79%","province_percentile_tooltip":"62% - 80%"},{"district":"Chilliwack","district_number":"033","year":"2017/2018","subpop":"Special Needs","records":"178","records2":"178","value":".75","value2":".75","tooltip":"75%","tooltipR":"         178","max":".75","min":".57","percentile_25":".56","percentile_75":".76","min_max_tooltip":"57% - 75%","province_percentile_tooltip":"56% - 76%"}],
            "transform": [
                {
                    "type": "formula",
                    "as": "records",
                    "expr": "format(datum.records,',.0f')"
                }
            ]
        },
        {
            "name": "distminmax",
            "source": "provlvl"
        },
        {
            "name": "distcurrent",
            "source": "provlvl"
        },
        {
            "name": "quartiles",
            "source": "provlvl"
        }
    ],
    "scales": [
        {
            "name": "layout",
            "type": "band",
            "range": "height",
            "domain": {
                "data": "provlvl",
                "field": "subpop"
            }
        },
        {
            "name": "xscale",
            "type": "linear",
            "range": "width",
            "round": false,
            "domain": {
                "data": "provlvl",
                "field": "value"
            },
            "zero": true,
            "nice": true,
            "domainMax": 1
        }
    ],
    "axes": [
        {
            "orient": "bottom",
            "scale": "xscale",
            "zindex": 1,
            "format": "%",
            "labelFontSize": 15,
            "labelAlign": "",
            "labelColor": "#898989",
            "labelFlush": true
        },
        {
            "orient": "left",
            "scale": "layout",
            "tickCount": 5,
            "zindex": 1,
            "labelFontSize": 15,
            "grid": false,
            "labelAlign": "left",
            "labelColor": "#666666",
            "tickSize": 230,
            "labelPadding": 0
        },
        {
            "orient": "top",
            "scale": "xscale",
            "zindex": 1,
            "format": "%",
            "labelFontSize": 15,
            "labelAlign": "",
            "labelColor": "#666666",
            "labelFlush": true,
            "offset": 0
        }
    ],
    "marks": [
        {
            "type": "group",
            "from": {
                "facet": {
                    "data": "quartiles",
                    "name": "province",
                    "groupby": "subpop"
                }
            },
            "encode": {
                "enter": {
                    "yc": {
                        "scale": "layout",
                        "field": "subpop",
                        "band": 0.5
                    },
                    "height": {
                        "signal": "plotWidth"
                    },
                    "width": {
                        "signal": "width"
                    }
                }
            },
            "data": [
                {
                    "name": "provsummary",
                    "source": "province",
                    "transform": [
                        {
                            "type": "aggregate",
                            "fields": [
                                "value",
                                "value"
                            ],
                            "ops": [
                                "q1",
                                "q3"
                            ],
                            "as": [
                                "q1",
                                "q3"
                            ]
                        }
                    ]
                }
            ],
            "marks": [
                {
                    "type": "rect",
                    "from": {
                        "data": "province"
                    },
                    "encode": {
                        "enter": {
                            "fill": {
                                "value": "#d9d9d9"
                            },
                            "cornerRadius": {
                                "value": 1
                            },
                            "tooltip": {
                                "signal": "{'Average 25th Percentile':format(datum.percentile_25, '0.0%'),'Average 75th Percentile':format(datum.percentile_75, '0.0%')}"
                            }
                        },
                        "update": {
                            "yc": {
                                "signal": "plotWidth / 2"
                            },
                            "height": {
                                "signal": "plotWidth / .775"
                            },
                            "x": {
                                "scale": "xscale",
                                "field": "percentile_25"
                            },
                            "x2": {
                                "scale": "xscale",
                                "field": "percentile_75"
                            },
                            "stroke": {
                                "value": null
                            },
                            "zindex": {
                                "value": 0
                            }
                        },
                        "hover": {
                            "stroke": {
                                "value": "grey"
                            },
                            "zindex": {
                                "value": 1
                            }
                        }
                    }
                }
            ]
        },
        {
            "type": "group",
            "from": {
                "facet": {
                    "data": "distcurrent",
                    "name": "current",
                    "groupby": "subpop"
                }
            },
            "encode": {
                "enter": {
                    "yc": {
                        "scale": "layout",
                        "field": "subpop",
                        "band": 0.5
                    },
                    "height": {
                        "signal": "plotWidth"
                    },
                    "width": {
                        "signal": "width"
                    }
                }
            },
            "data": [
                {
                    "name": "summarycurrent",
                    "source": "current"
                }
            ],
            "marks": [
                {
                    "type": "symbol",
                    "from": {
                        "data": "summarycurrent"
                    },
                    "encode": {
                        "enter": {
                            "description": "below command turns off symbol if value is set to 'MSK'",
                            "fill": [
                                {
                                    "test": "datum['value'] =='MSK'",
                                    "value": ""
                                },
                                {
                                    "value": "#f28e2b"
                                }
                            ],
                            "width": {
                                "value": 2
                            },
                            "size": {
                                "value": 460
                            },
                            "tooltip": {
                                "signal": "{'Student Group':datum.subpop,'Completion Rate':datum.tooltip,'School Year':datum.year,'Records':datum.tooltipR,'5-year range for this district':datum.min_max_tooltip,'Typical range across B.C. (middle 50% of districts)':datum.province_percentile_tooltip}"
                            }
                        },
                        "update": {
                            "yc": {
                                "signal": "plotWidth / 2"
                            },
                            "height": {
                                "signal": "plotWidth / 50"
                            },
                            "x": {
                                "aggregate": "value",
                                "scale": "xscale",
                                "field": "value"
                            },
                            "stroke": {
                                "value": null
                            },
                            "zindex": {
                                "value": 0
                            }
                        },
                        "hover": {
                            "stroke": {
                                "value": "black"
                            },
                            "zindex": {
                                "value": 0
                            }
                        }
                    }
                }
            ]
        },
        {
            "type": "group",
            "from": {
                "facet": {
                    "data": "distminmax",
                    "name": "district",
                    "groupby": "subpop"
                }
            },
            "encode": {
                "enter": {
                    "yc": {
                        "scale": "layout",
                        "field": "subpop",
                        "band": 0.5
                    },
                    "height": {
                        "signal": "plotWidth"
                    },
                    "width": {
                        "signal": "width"
                    }
                }
            },
            "data": [
                {
                    "name": "distsummary",
                    "source": "district",
                    "transform": [
                        {
                            "type": "aggregate",
                            "fields": [
                                "value",
                                "value"
                            ],
                            "ops": [
                                "min",
                                "max"
                            ],
                            "as": [
                                "min",
                                "max"
                            ]
                        }
                    ]
                }
            ],
            "marks": [
                {
                    "type": "rect",
                    "from": {
                        "data": "district"
                    },
                    "encode": {
                        "enter": {
                            "fill": {
                                "value": "#f28e2b"
                            },
                            "tooltip": {
                                "signal": "{'Student Group':datum.subpop,'Completion Rate':datum.tooltip,'School Year':datum.year,'Records':datum.tooltipR,'5-year range for this district':datum.min_max_tooltip,'Typical range across B.C. (middle 50% of districts)':datum.province_percentile_tooltip}"
                            }
                        },
                        "update": {
                            "yc": {
                                "signal": "plotWidth / 2",
                                "offset": -0.5
                            },
                            "x": {
                                "scale": "xscale",
                                "field": "min"
                            },
                            "x2": {
                                "scale": "xscale",
                                "field": "max"
                            },
                            "stroke": {
                                "value": null
                            },
                            "zindex": {
                                "value": 10
                            },
                            "height": {
                                "value": 4.5
                            }
                        },
                        "hover": {
                            "stroke": {
                                "value": "black"
                            },
                            "strokeWidth": {
                                "value": 1
                            },
                            "zindex": {
                                "value": 10
                            },
                            "height": {
                                "value": 4
                            }
                        }
                    }
                }
            ]
        },
        {
            "type": "group",
            "from": {
                "facet": {
                    "data": "distcurrent",
                    "name": "rec",
                    "groupby": "subpop"
                }
            },
            "encode": {
                "enter": {
                    "yc": {
                        "scale": "layout",
                        "field": "subpop",
                        "band": 0.5
                    },
                    "height": {
                        "signal": "plotWidth"
                    },
                    "width": {
                        "signal": "width"
                    }
                }
            },
            "data": [
                {
                    "name": "reccurrent",
                    "source": "rec"
                }
            ],
            "marks": [
                {
                    "type": "text",
                    "from": {
                        "data": "reccurrent"
                    },
                    "encode": {
                        "enter": {
                            "text": {
                                "field": "records"
                            },
                            "fontSize": {
                                "value": 15
                            },
                            "fill": [
                                {
                                    "test": "datum['records'] =='NaN'",
                                    "value": ""
                                },
                                {
                                    "value": "#898989"
                                }
                            ]
                        },
                        "update": {
                            "yc": {
                                "signal": "plotWidth / 1.5"
                            },
                            "height": {
                                "signal": "plotWidth / 50"
                            },
                            "x": {
                                "offset": -80
                            },
                            "stroke": {
                                "value": null
                            },
                            "zindex": {
                                "value": 0
                            }
                        }
                    }
                }
            ]
        },
        {
            "type": "group",
            "from": {
                "facet": {
                    "data": "distcurrent",
                    "name": "rec",
                    "groupby": "subpop"
                }
            },
            "encode": {
                "enter": {
                    "yc": {
                        "scale": "layout",
                        "field": "subpop",
                        "band": 0.5
                    },
                    "height": {
                        "signal": "plotWidth"
                    },
                    "width": {
                        "signal": "width"
                    }
                }
            },
            "data": [
                {
                    "name": "reccurrent",
                    "source": "rec"
                }
            ],
            "marks": [
                {
                    "type": "text",
                    "from": {
                        "data": "reccurrent"
                    },
                    "encode": {
                        "enter": {
                            "text": {
                                "field": "records2"
                            },
                            "fontSize": {
                                "value": 15
                            },
                            "fill": [
                                {
                                    "test": "datum['records2'] !=='MSK'",
                                    "value": ""
                                },
                                {
                                    "value": "#898989"
                                }
                            ]
                        },
                        "update": {
                            "yc": {
                                "signal": "plotWidth / 1.5"
                            },
                            "height": {
                                "signal": "plotWidth / 50"
                            },
                            "x": {
                                "offset": -80
                            },
                            "stroke": {
                                "value": null
                            },
                            "zindex": {
                                "value": 0
                            }
                        }
                    }
                }
            ]
        }
    ]
}
    },
    async draw(){
      let struct = this.def
      //struct.width = 'container'
      //struct.height = 'container'
      await embed('#viz', struct, {actions:false})
    }
  }
}
</script>

<style scoped>

</style>