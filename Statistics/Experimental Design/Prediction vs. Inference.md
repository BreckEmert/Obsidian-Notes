This is a rough topic, but for the majority of cases, inference is about discovering intrinsic relationships in data.  Prediction is about relying on those inferences we made (the parameters) to generalize to new situations.

We might find ourselves looking at the tornadoes of Kansas, building a model with a set of features.  Here, we assume there is some proportion of the variance that we'll be able to explain with the features we have, and we search model-space by various ways of seeing what minimizes a loss function.  As such, a good model will be a set of estimated parameters that relates the features to occurrences of tornadoes.

Prediction in this context is, of course, looking at when and where the next tornado will happen.  Here, we know prediction must be distinct, because some notable problems go away:

- When trying to isolate effects into individual variables, we _really_ hated multicollinearity.  It doesn't allow us to properly see where the true effect comes from.  If we wanted to see whether a hot day will lead to a tornado, we might not know if the humidity is highly correlated 
- But when predicting, it doesn't matter which variable took on the highest weight.  If we have wind speed in km/h **and** wind speed in m/h in our inferential model, then it had to assign the true relationship to both of those parameters.  But the sum during our prediction will be the same and the prediction will be unchanged.  It was only our inference about the relationships that was affected.

tldr; inference is more important when you want to isolate effects and relationships.  Knowing what relates to what is highly important for something like determining the most effective intervention in healthcare, or what drives satisfaction in a business.