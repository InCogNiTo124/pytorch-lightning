.. testsetup:: *

    from torch.nn import Module
    from pytorch_lightning.core.lightning import LightningModule
    from pytorch_lightning.metrics import TensorMetric, NumpyMetric

Metrics
=======
This is a general package for PyTorch Metrics. These can also be used with regular non-lightning PyTorch code.
Metrics are used to monitor model performance.

In this package we provide two major pieces of functionality.

1. A collection of ready to use pupular metrics. There are two types of metrics: Class metrics and Functional metrics.
2. A Metric class you can use to implement custom metrics with built-in distributed (DDP) support which are device agnostic.


--------------

Class Metrics
-------------
Class metrics can be instantiated as part of a module definition (even with just
plain PyTorch).

.. testcode::

    from pytorch_lightning.metrics import Accuracy

    # Plain PyTorch
    class MyModule(Module):
        def __init__(self):
            super().__init__()
            self.metric = Accuracy()

        def forward(self, x, y):
            y_hat = ...
            acc = self.metric(y_hat, y)

    # PyTorch Lightning
    class MyModule(LightningModule):
        def __init__(self):
            super().__init__()
            self.metric = Accuracy()

        def training_step(self, batch, batch_idx):
            x, y = batch
            y_hat = ...
            acc = self.metric(y_hat, y)

These metrics even work when using distributed training:

.. code-block:: python

    model = MyModule()
    trainer = Trainer(gpus=8, num_nodes=2)

    # any metric automatically reduces across GPUs (even the ones you implement using Lightning)
    trainer.fit(model)

Accuracy
^^^^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.Accuracy
    :noindex:

AveragePrecision
^^^^^^^^^^^^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.AveragePrecision
    :noindex:

AUROC
^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.AUROC
    :noindex:

ConfusionMatrix
^^^^^^^^^^^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.ConfusionMatrix
    :noindex:

DiceCoefficient
^^^^^^^^^^^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.DiceCoefficient
    :noindex:

F1
^^

.. autoclass:: pytorch_lightning.metrics.classification.F1
    :noindex:

FBeta
^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.FBeta
    :noindex:

PrecisionRecall
^^^^^^^^^^^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.PrecisionRecall
    :noindex:

Precision
^^^^^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.Precision
    :noindex:

Recall
^^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.Recall
    :noindex:

ROC
^^^

.. autoclass:: pytorch_lightning.metrics.classification.ROC
    :noindex:

MulticlassROC
^^^^^^^^^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.MulticlassROC
    :noindex:

MulticlassPrecisionRecall
^^^^^^^^^^^^^^^^^^^^^^^^^

.. autoclass:: pytorch_lightning.metrics.classification.MulticlassPrecisionRecall
    :noindex:

--------------

Functional Metrics
------------------

Usage Example::

    from pytorch_lightning.metrics.functional import accuracy

    pred = torch.tensor([0, 1, 2, 3])
    target = torch.tensor([0, 1, 2, 2])

    # calculates accuracy across all GPUs and all Nodes used in training
    accuracy(pred, target)

Out::

    tensor(0.7500)

accuracy (F)
^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.accuracy
    :noindex:

auc (F)
^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.auc
    :noindex:

auroc (F)
^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.auroc
    :noindex:

average_precision (F)
^^^^^^^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.average_precision
    :noindex:

confusion_matrix (F)
^^^^^^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.confusion_matrix
    :noindex:

dice_score (F)
^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.dice_score
    :noindex:

f1_score (F)
^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.f1_score
    :noindex:

fbeta_score (F)
^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.fbeta_score
    :noindex:

multiclass_precision_recall_curve (F)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.multiclass_precision_recall_curve
    :noindex:

multiclass_roc (F)
^^^^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.multiclass_roc
    :noindex:

precision (F)
^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.precision
    :noindex:

precision_recall (F)
^^^^^^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.precision_recall
    :noindex:

precision_recall_curve (F)
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.precision_recall_curve
    :noindex:

recall (F)
^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.recall
    :noindex:

roc (F)
^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.roc
    :noindex:

stat_scores (F)
^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.stat_scores
    :noindex:

stat_scores_multiple_classes (F)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.stat_scores_multiple_classes
    :noindex:

----------------

Metric pre-processing
---------------------
Metric

to_categorical (F)
^^^^^^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.to_categorical
    :noindex:

to_onehot (F)
^^^^^^^^^^^^^

.. autofunction:: pytorch_lightning.metrics.functional.to_onehot
    :noindex:


--------------

Implementing a custom metric
------------------
You can implement metrics as either a PyTorch metric or a Numpy metric (PyTorch metrics
are preferred , as Numpy metrics slow down training).

Use :class:`TensorMetric` to implement native PyTorch metrics. This class
handles automated DDP syncing and converts all inputs and outputs to tensors.

Use :class:`NumpyMetric` to implement Numpy metrics. This class
handles automated DDP syncing and converts all inputs and outputs to tensors.

.. warning::
    Numpy metrics might slow down your training substantially,
    since every metric computation requires a GPU sync to convert tensors to Numpy.

TensorMetric
^^^^^^^^^^^^
Here's an example showing how to implement a TensorMetric

.. testcode::

    class RMSE(TensorMetric):
        def forward(self, x, y):
            return torch.sqrt(torch.mean(torch.pow(x-y, 2.0)))

.. autoclass:: pytorch_lightning.metrics.metric.TensorMetric
    :noindex:

NumpyMetric
^^^^^^^^^^^
Here's an example showing how to implement a NumpyMetric

.. testcode::

    class RMSE(NumpyMetric):
        def forward(self, x, y):
            return np.sqrt(np.mean(np.power(x-y, 2.0)))
        

.. autoclass:: pytorch_lightning.metrics.metric.NumpyMetric
    :noindex:
