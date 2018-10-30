.. _effect-details/working-with-paths:

Working With Paths
################################################################################

Accessing Path Data
================================================================================

Paths differ from other parameter types, in that their values are not directly accessible. In addition to checking them out and in (like layer parameters), you must use our path data function suites to obtain the details of the path at a given time. See :ref:`effect-details/working-with-paths.PF_PathQuerySuite` and :ref:`effect-details/working-with-paths.PF_PathDataSuite`. Never use the values present in a path parameter when it's passed to you, without first checking it out; while deleted paths will not be available, further updating is done "lazily" (later); your effect won't see these changes unless it checks out the path.

----

Manipulating Path Data
================================================================================

You can also use the :ref:`aegps/aegp-suites.AEGP_MaskOutlineSuite` to manipulate paths. See :ref:`aegps/cheating-effect-usage-of-aegp-suites`. Path parameters are treated as opaque blobs of data; get and set functions must be used to access and manipulate them. Like layer parameters, they must be checked out (and in!) by effects which access them.

----

Vertices
================================================================================

Path vertices are more complex than simple points. All member variables are PF_FpLongs (doubles), and are in the layer's coordinate space.

----

PF_PathVertex
================================================================================

+---------------+-----------------------------+
|  **Member**   |       **Description**       |
+===============+=============================+
| ``x``         | The location of the vertex. |
+---------------+-----------------------------+
| ``y``         |                             |
+---------------+-----------------------------+
| ``tan_in_x``  | The incoming tangent point. |
+---------------+-----------------------------+
| ``tan_in_y``  |                             |
+---------------+-----------------------------+
| ``tan_out_x`` | The outgoing tangent point. |
+---------------+-----------------------------+
| ``tan_out_y`` |                             |
+---------------+-----------------------------+

----

.. _effect-details/working-with-paths.PF_PathDataSuite:

PF_PathDataSuite
================================================================================

This suite provides information about paths (sequences of vertices).

+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
|          **Function**          |                                                  **Description**                                                  |
+================================+===================================================================================================================+
| ``PF_PathIsOpen``              | Returns ``TRUE`` if the path is not closed (if the beginning and end vertex are not identical).                   |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathIsOpen(                                                                                                  |
|                                |     PF_ProgPtr         effect_ref0,                                                                               |
|                                |     PF_PathOutlinePtr  pathP,                                                                                     |
|                                |     PF_Boolean         *openPB);                                                                                  |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathNumSegments``         | Retrieves the number of segments in the path.                                                                     |
|                                | N segments means there are segments ``[0.N-1];``                                                                  |
|                                | segment J is defined by vertex ``J`` and ``J+1``.                                                                 |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathNumSegments(                                                                                             |
|                                |     PF_ProgPtr         effect_ref0,                                                                               |
|                                |     PF_PathOutlinePtr  pathP,                                                                                     |
|                                |     A_long             *num_segmentsPL);                                                                          |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathVertexInfo``          | Retrieves the ``PF_PathVertex`` for the specified path.                                                           |
|                                | The range of points is ``[0.num_segments];``                                                                      |
|                                | for closed paths, ``vertex[0] == vertex[num_segments]``.                                                          |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathVertexInfo(                                                                                              |
|                                |     PF_ProgPtr         effect_ref0,                                                                               |
|                                |     PF_PathOutlinePtr  pathP,                                                                                     |
|                                |     A_long             which_pointL,                                                                              |
|                                |     PF_PathVertex      *vertexP);                                                                                 |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathPrepareSegLength``    | This fairly counter-intuitive function informs After Effects that you're going to ask for the length of a segment |
|                                | (using ``PF_PathGetSegLength`` below), and it'd better get ready.                                                 |
|                                | ``frequencyL`` indicates how many times you'd like us to sample the length; our internal effects use 100.         |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathPrepareSegLength(                                                                                        |
|                                |     PF_ProgPtr         effect_ref0,                                                                               |
|                                |     PF_PathOutlinePtr  pathP,                                                                                     |
|                                |     A_long             which_segL,                                                                                |
|                                |     A_long             frequencyL,                                                                                |
|                                |     PF_PathSegPrepPtr  *lengthPrepPP);                                                                            |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathGetSegLength``        | Retrieves the length of the given segment.                                                                        |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathGetSegLength(                                                                                            |
|                                |     PF_ProgPtr         effect_ref0,                                                                               |
|                                |     PF_PathOutlinePtr  pathP,                                                                                     |
|                                |     A_long             which_segL,                                                                                |
|                                |     PF_PathSegPrepPtr  *lengthPrepP0,                                                                             |
|                                |     PF_FpLong          *lengthPF);                                                                                |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathEvalSegLength``       | Retrieves the location of a point lengthF along the given path segment.                                           |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathEvalSegLength(                                                                                           |
|                                |     PF_ProgPtr         effect_ref0,                                                                               |
|                                |     PF_PathOutlinePtr  pathP,                                                                                     |
|                                |     PF_PathSegPrepPtr  *lengthPrepPP0,                                                                            |
|                                |     A_long             which_segL,                                                                                |
|                                |     PF_FpLong          lengthF,                                                                                   |
|                                |     PF_FpLong          *x,                                                                                        |
|                                |     PF_FpLong          *y);                                                                                       |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathEvalSegLengthDeriv1`` | Retrieves the location, and the first derivative, of a point ``lengthF`` along the given path segment.            |
|                                | If you're not sure why you'd ever need this, don't use it. Math is hard.                                          |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathEvalSegLengthDeriv1(                                                                                     |
|                                |     PF_ProgPtr         effect_ref0,                                                                               |
|                                |     PF_PathOutlinePtr  pathP,                                                                                     |
|                                |     PF_PathSegPrepPtr  *lengthPrepPP0,                                                                            |
|                                |     A_long             which_segL,                                                                                |
|                                |     PF_FpLong          lengthF,                                                                                   |
|                                |     PF_FpLong          *x,                                                                                        |
|                                |     PF_FpLong          *y,                                                                                        |
|                                |     PF_FpLong          *deriv1x,                                                                                  |
|                                |     PF_FpLong          *deriv1y);                                                                                 |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathCleanupSegLength``    | Call this when you're finished evaluating that segment length,                                                    |
|                                | so After Effects can properly clean up the ``PF_PathSegPrepPtr``.                                                 |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathCleanupSegLength(                                                                                        |
|                                |     PF_ProgPtr         effect_ref0,                                                                               |
|                                |     PF_PathOutlinePtr  pathP,                                                                                     |
|                                |     A_long             which_segL,                                                                                |
|                                |     PF_PathSegPrepPtr  *lengthPrepPP);                                                                            |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathIsInverted``          | Returns ``TRUE`` if the path is inverted.                                                                         |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathIsInverted(                                                                                              |
|                                |     PF_ProgPtr  effect_ref,                                                                                       |
|                                |     PF_PathID   unique_id,                                                                                        |
|                                |     PF_Boolean  *invertedB);                                                                                      |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathGetMaskMode``         | Retrieves the mode for the given path.                                                                            |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathGetMaskMode(                                                                                             |
|                                |     PF_ProgPtr   effect_ref,                                                                                      |
|                                |     PF_PathID    unique_id,                                                                                       |
|                                |     PF_MaskMode  *modeP);                                                                                         |
|                                |                                                                                                                   |
|                                | Mask mode is one of the following:                                                                                |
|                                |                                                                                                                   |
|                                | - ``PF_MaskMode_NONE``                                                                                            |
|                                | - ``PF_MaskMode_ADD``                                                                                             |
|                                | - ``PF_MaskMode_SUBTRACT``                                                                                        |
|                                | - ``PF_MaskMode_INTERSECT``                                                                                       |
|                                | - ``PF_MaskMode_LIGHTEN``                                                                                         |
|                                | - ``PF_MaskMode_DARKEN``                                                                                          |
|                                | - ``PF_MaskMode_DIFFERENCE``                                                                                      |
|                                | - ``PF_MaskMode_ACCUM``                                                                                           |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+
| ``PF_PathGetName``             | Retrieves the name of the path (up to ``PF_MAX_PATH_NAME_LEN`` long).                                             |
|                                |                                                                                                                   |
|                                | ::                                                                                                                |
|                                |                                                                                                                   |
|                                |   PF_PathGetName(                                                                                                 |
|                                |     PF_ProgPtr  effect_ref,                                                                                       |
|                                |     PF_PathID   unique_id,                                                                                        |
|                                |     A_char      *nameZ);                                                                                          |
+--------------------------------+-------------------------------------------------------------------------------------------------------------------+

----

.. _effect-details/working-with-paths.PF_PathQuerySuite:

PF_PathQuerySuite1
================================================================================

This suite is used to identify and access the paths associated with the effect's source layer.

+---------------------+----------------------------------------------------------------------------------------------------------+
|    **Function**     |                                               **Purpose**                                                |
+=====================+==========================================================================================================+
| ``PF_NumPaths``     | Retrieves the number of paths associated with the effect's source layer.                                 |
|                     |                                                                                                          |
|                     | ::                                                                                                       |
|                     |                                                                                                          |
|                     |   PF_NumPaths(                                                                                           |
|                     |     PF_ProgPtr  effect_ref,                                                                              |
|                     |     A_long      *num_pathsPL);                                                                           |
+---------------------+----------------------------------------------------------------------------------------------------------+
| ``PF_PathInfo``     | Retrieves the PF_PathID for the specified path.                                                          |
|                     |                                                                                                          |
|                     | ::                                                                                                       |
|                     |                                                                                                          |
|                     |   PF_PathInfo(                                                                                           |
|                     |     PF_ProgPtr  effect_ref,                                                                              |
|                     |     A_long      indexL,                                                                                  |
|                     |     PF_PathID   *unique_idP);                                                                            |
+---------------------+----------------------------------------------------------------------------------------------------------+
| ``PF_CheckoutPath`` | Acquires the PF_PathOutlinePtr for the path at the specified time.                                       |
|                     |                                                                                                          |
|                     | ::                                                                                                       |
|                     |                                                                                                          |
|                     |   PF_CheckoutPath(                                                                                       |
|                     |     PF_ProgPtr         effect_ref,                                                                       |
|                     |     PF_PathID          unique_id,                                                                        |
|                     |     A_long             what_time,                                                                        |
|                     |     A_long             time_step,                                                                        |
|                     |     A_u_long           time_scale,                                                                       |
|                     |     PF_PathOutlinePtr  *pathPP);                                                                         |
+---------------------+----------------------------------------------------------------------------------------------------------+
| ``PF_CheckinPath``  | Releases the path back to After Effects. Always do this, regardless of any error conditions encountered. |
|                     | Every checkout must be balanced by a checkin, or pain will ensue.                                        |
|                     |                                                                                                          |
|                     | ::                                                                                                       |
|                     |                                                                                                          |
|                     |   PF_CheckinPath(                                                                                        |
|                     |     PF_ProgPtr         effect_ref,                                                                       |
|                     |     PF_PathID          unique_id,                                                                        |
|                     |     PF_Boolean         changedB,                                                                         |
|                     |     PF_PathOutlinePtr  pathP);                                                                           |
+---------------------+----------------------------------------------------------------------------------------------------------+




