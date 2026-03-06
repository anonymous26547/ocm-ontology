
# Truth Values of RegulatedAction

# OnTimeExecutedAction 
numericAtTime(elapse, ?te) ^ Obligation(?o) ^ obligationContent(?o, ?ta) ^ TemporalAction(?ta) ^ numericStartTime(?ta, ?st) ^ numericDeadline(?ta, ?d) ^ actionContent(?ta, ?ac) ^ RegulatedAction(?ac) ^ numericAtTime(?ac, ?t) ^ swrlb:lessThanOrEqual (?t,?te) ^ swrlb:lessThanOrEqual (?st, ?t) ^ swrlb:lessThan (?t, ?d) -> gem:OnTimeExecutedAction(?ac)

## KOnTimeExecutedAction 

assert KOnTimeExecutedAction equivalentTo oneOf {ac1, ... acn} with {ac1,...acn} = retrieve (OnTimeExecutedAction) 

## NotKOnTimeExecutedAction  

gem:NotKOnTimeExecutedAction a owl:Class ;
    owl:equivalentClass [
        a owl:Class ;
        owl:intersectionOf (
            :RegulatedAction
            [ a owl:Class ;
              owl:complementOf gem:KOnTimeExecutedAction
            ]
        )
    ] .
### Important note: one needs to add that the instances belonging to NotKOnTimeExecutedAction are differentFrom  KOnTimeExecutedAction. --> All regulationActions are different from each other


# FailedExecutedAction 

numericAtTime (elapse, ?t) ^ Obligation (?o) ^ obligationContent (?o, ?ta) ^ TemporalAction (?ta) ^  numericDeadline (?ta, ?d) ^ swrlb:lessThan(?d,?t) ^  actionContent(?ta, ?ac) ^ RegulatedAction(?ac) 
^ gem:NotKOnTimeExecutedAction (?ac) ->  gem:FailedExecutedAction  (?ac)

# ActiveTemporalAction 

:numericAtTime (elapse, ?t) ^ :Obligation (?o) :obligationContent (?o, ?ta) ^ :TemporalAction (?ta) ^ :numericStartTime (?ta, ?st) ^ :numericDeadline (?ta, ?d)  ^  swrlb:lessThanOrEqual(?st,?t)  ^   swrlb:lessThan(?t,?d) -> gem:ActiveTemporalAction  (?ta)

# ExpiredTemporalAction

:numericAtTime (elapse, ?t) ^ :Obligation (?o) ^ :obligationContent (?o, ?ta) ^ :TemporalAction (?ta) ^ :numericDeadline (?ta, ?d) ^  swrlb:lessThan(?d,?t)
->  gem:ExpiredTemporalAction  (?ta)

# TriggeredEvent (Truth values of the activation condition)

Obligation(?o) ^  activationCondition (?o, ?ac) ^ :numericAtTime (?ac, ?te)  -> gem:TriggeredEvent(?ac)



