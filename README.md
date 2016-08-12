k5-spec
=======

Idea here is pretty simple.  Encapsulate the details of a trained neural network into a on-disk format. The network should be human readable, as in https://github.com/jsongraph/json-graph-specification:

    {
        "graph": {
            "nodes": [
                {
                    "id": "A",
                },
                {
                    "id": "B",
                }
            ],
            "edges": [
                {
                    "source": "A",
                    "target": "B"
                }
            ]
        }
    }

Coupled with this format are a TENSOR (think matrix or vector but N-dimentional) of weights that correspond to each layer or "node" in the network above.  These weights could be fp16 fp32 or fp64.  These need to be stored with the network above but weights are not human readable.  Which is why we can just cram all these data structures into the widely supported HDF5 format (https://www.hdfgroup.org/HDF5/)

So if we were to look at a K5 file, it would look something like:

    $ h5ls working/mnist.k5

    /                        Group
    /author                  Dataset {25}
    /description             Dataset {82}

    /model                   Group
    /model/network           Dataset {1484}
    /model/weights           Group
    /model/weights/A         Dataset {10000, 1}
    /model/weights/B         Dataset {10000, 1}

    /validate                Group
    /validate/input          Dataset {32,32}
    /validate/output         Dataset {10,1}

    /training                Group
    /training/data           Dataset {60000/Inf, 32, 32}
    /training/labels         Dataset {60000, 1}

    /evaluate                Group
    /evaluate/data           Dataset {10000/Inf, 32, 32}
    /evaluate/labels         Dataset {10000, 1}


In the above example, we have 4 distinct parts:

  - Metadata
  - The model (network as JSON and weights separated by layers)
  - Just one input and output to validate that the network was correctly loaded
  - Training data
  - Testing data
